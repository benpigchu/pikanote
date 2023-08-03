---
title: "在 Unity 的 URP 中实现屏幕空间描边效果"
image: /assets/img/cover/unity-urp-outline.png
zhihu_link: https://zhuanlan.zhihu.com/p/647878652
date: "2023-08-04 01:02:27 +0800"
---

> 题图为最终实现的描边效果

最近因为想要在一个私下的 Unity 项目中改善一下已选中物体的高亮效果，所以想要实现给物体描边的效果。由于我希望同时选中不同物体的时候不同选中的物体之间不描边，所以使用屏幕空间的描边而不是对各个物体分别描边会比较好。当然，借此机会也能熟悉一下对 URP 进行扩展的方法。接下来就来大概介绍一下这个效果是如何实现的。

本文中使用的 Unity 版本是 2023.1.5f1，对应的 Universal RP 包和 Core RP Library 包版本是 15.0.6。不排除本文中提到的技术细节将来出现变化的可能性。

## 基本实现思路

稍微熟悉一点图形学的都应当知道，最基本的屏幕空间描边的实现步骤大致如下：

- 把需要描边的物体单独渲染到一张贴图里，作为要描边的内容的 Mask
- 用一个 Shader，对每个像素附近的一些像素进行采样，如果在 Mask 中则在对应像素画上描边

接下来介绍如何在 Unity 的 URP 的框架中，使用 `ScriptableRendererFeature` 实现这个效果。

## 建立自定义的 `ScriptableRendererFeature`

按照 [Unity 的相关文档](https://docs.unity3d.com/Packages/com.unity.render-pipelines.universal@16.0/manual/renderer-features/create-custom-renderer-feature.html){: target="_blank" rel="noopener"}，我们应当编写一个继承了 `ScriptableRendererFeature` 的类来对 URP 的渲染器提供功能扩展。

我们在自己的 `ScriptableRendererFeature` 需要重写的方法包括：
- `Create`：在渲染管线初始化时调用，用来创建需要用到的 `ScriptableRenderPass`。
`ScriptableRenderPass` 的用法将会在本文之后的部分介绍
- `AddRenderPasses`：在每帧渲染开始时调用，用于将之前创建的 `ScriptableRenderPass` 放入本帧的渲染队列中，让渲染器之后执行它
- `SetupRenderPasses`：在每帧渲染开始时调用，用于配置之前创建的 `ScriptableRenderPass`。这一步和前面的 `AddRenderPasses` 分开的原因在文档中讲得不是很清楚，似乎是渲染相关的一些资源（比如 Camera 用到的 RenderTarget 什么的）要到这一步才能拿到

另外，`ScriptableRendererFeature` 编写完成之后，我们可以在编辑器中将其加入到 URP 渲染器里，并对其进行一些配置，具体的操作请看 [Unity 的相关文档](https://docs.unity3d.com/Packages/com.unity.render-pipelines.universal@16.0/manual/urp-renderer-feature-how-to-add.html){: target="_blank" rel="noopener"}。`ScriptableRendererFeature` 继承了 `ScriptableObject`，所以我们可以用和 `ScriptableObject` 一样的方式自定义它的编辑器。

在我们的用例当中，我们可以配置的内容包括：
- 用来选出需要高亮的物体的层
- 用来描边的 Shader
- 选区的颜色和描边的颜色
- 用来确定描边宽度的参数

我们的实现如下：
```cs
public class SelectionRendering : ScriptableRendererFeature
{
	SelectionPass selectionPass;
	public Shader SelectionBlitShader;
	public Color OutlineColor;
	public Color SelectionColor;
	public float SampleDistance;
	public float SampleDistanceReferenceWidth;

	public uint RenderingLayerMask = uint.MaxValue;

	public override void Create()
	{
		if (SelectionBlitShader == null)
		{
			SelectionBlitShader = Shader.Find("SelectionRendering/SelectionBlit");
		}
		// 建立对应的 ScriptableRenderPass
		selectionPass = new SelectionPass(this);
		// 将 ScriptableRenderPass 的渲染时机指定为所有其他渲染操作完成之后
		selectionPass.renderPassEvent = RenderPassEvent.AfterRendering;
	}

	public override void AddRenderPasses(ScriptableRenderer renderer, ref RenderingData renderingData)
	{
		// 在编辑器中不渲染描边
		if (renderingData.cameraData.cameraType == CameraType.Game)
		{
			// 把创建的 ScriptableRenderPass 放入渲染队列
			renderer.EnqueuePass(selectionPass);
		}
	}
	public override void SetupRenderPasses(ScriptableRenderer renderer, in RenderingData renderingData)
	{
		if (renderingData.cameraData.cameraType == CameraType.Game)
		{
			// 把当前相机的 RenderTarget 传给创建的 ScriptableRenderPass
			selectionPass.SetTarget(renderer.cameraColorTargetHandle);
		}
	}
}
```

## 编写自定义的 `ScriptableRenderPass`

`ScriptableRenderPass` 是实际执行渲染操作的类。我们继承此类时需要重载的方法如下：
- `OnCameraSetup`：此方法在相机初始化时调用，用于初始化相关的 RenderTarget 等资源，并进行一些配置
- `Execute`：真正的渲染过程

在实现具体的功能之前我们需要一些组件，接下来逐一进行介绍。

在我们的实现思路中，我们需要一个中间的贴图。在 Unity 的可编程渲染管线这一套里面，我们应当使用 `RTHandle` 来管理 RenderTarget。Universal RP 包提供了 `RenderingUtils.ReAllocateIfNeeded` 方法来按需分配和重新分配我们需要的 `RTHandle` ，只需要传入 `RenderTextureDescriptor` 指定 RenderTarget 的格式即可。在 `OnCameraSetup` 方法中，我们可以从 `renderingData` 参数中获得相机信息，再从中获得本次渲染的相机的 RenderTarget 的以 `RenderTextureDescriptor`  表示的格式，基于这些信息我们就可以维护我们需要的中间贴图了。

那么接下来我们需要将需要描边的物体渲染到中间贴图上。这里我们需要通过 `Execute` 的参数中传进来的 `context` 参数上的 `CreateRendererList` 创建一个用来表示物体绘制过程的 `RendererList`， 然后将其提交到 `CommandBuffer` 中执行。`RendererList` 的创建需要用到 `RendererListParams`，在其中包含了用 `CullingResults` 表示的剔除操作的结果，用来指定绘制方式的 `DrawingSettings`，和用于筛选物体的 `FilteringSettings`。其中 `CullingResults` 可以直接从 `renderingData` 参数获取，剩下两个需要我们手动构建。

要构建 `FilteringSettings`，我们只需要指定要渲染的物体在渲染队列中的范围，物体所在的层，物体所在的渲染层即可。注意层和渲染层是两个不同的东西，物体的层也会被物理引擎等其他子系统用到，但是渲染层只被渲染引擎关心。同时，一个物体可以同时属于多个渲染层。

要构建 `DrawingSettings`，我们需要通过一个 `ShaderTagId` 的列表指定使用哪些 Shader Pass 进行绘制，然后从 `renderingData` 中获取指定物体渲染顺序的 `SortingSettings` 即可。除此之外我们也可以覆盖物体的材质等等，但这里我们没有使用这个功能。

将这些配置都设置好之后，我们便可以把选中物体的 Mask 渲染出来，然后用它渲染描边了。这里需要使用 `Blitter.BlitCameraTexture` 方法把我们的 Mask 材质经过我们自定义材质覆盖到相机已经渲染好的内容上，然后我们就完成了描边的渲染。

我们的实现如下，刚才的描述中没有顾及到的细节在注释中给出：
```cs
class SelectionPass : ScriptableRenderPass
{
	public static readonly int SampleDistanceShaderId = Shader.PropertyToID("_SampleDistance");
	public static readonly int BlitTextureSizeShaderId = Shader.PropertyToID("_BlitTextureSize");
	public static readonly int SelectionColorShaderId = Shader.PropertyToID("_SelectionColor");
	public static readonly int OutlineShaderId = Shader.PropertyToID("_OutlineColor");
	public SelectionRendering feature;
	private RTHandle selection;
	private RTHandle selectionDepth;
	private RTHandle cameraColor;
	private List<ShaderTagId> shaderTagIdList = new List<ShaderTagId> {
		new ShaderTagId("UniversalForward"),
		new ShaderTagId("UniversalForwardOnly"),
		new ShaderTagId("LightweightForward"),
		new ShaderTagId("SRPDefaultUnlit")
	};

	public Material selectionBlitMaterial;

	private static readonly ShaderTagId SelectionShaderTagId = new ShaderTagId("Selection");

	public SelectionPass(SelectionRendering feature)
	{
		this.feature = feature;
		// 在构造函数中创建描边时使用的材质
		this.selectionBlitMaterial = CoreUtils.CreateEngineMaterial(feature.SelectionBlitShader);
		profilingSampler = new ProfilingSampler("SelectionPass");
	}

	public void SetTarget(RTHandle cameraColor)
	{
		this.cameraColor = cameraColor;
	}

	public override void OnCameraSetup(CommandBuffer cmd, ref RenderingData renderingData)
	{
		RenderTextureDescriptor descriptor = renderingData.cameraData.cameraTargetDescriptor;
		descriptor.colorFormat = RenderTextureFormat.ARGB32;
		// 分配深度 RenderTexture
		RenderingUtils.ReAllocateIfNeeded(ref selectionDepth, descriptor, wrapMode: TextureWrapMode.Clamp, name: "_selection");
		// 分配颜色 RenderTexture
		descriptor.depthBufferBits = 0;
		RenderingUtils.ReAllocateIfNeeded(ref selection, descriptor, wrapMode: TextureWrapMode.Clamp, name: "_selection");

		// 设置描边材质的信息，这里我希望描边宽度与分辨率成正比所以做了一些计算
		selectionBlitMaterial.SetFloat(SampleDistanceShaderId, feature.SampleDistance * ((float)descriptor.width) / feature.SampleDistanceReferenceWidth);
		selectionBlitMaterial.SetVector(BlitTextureSizeShaderId, new Vector4(descriptor.width, descriptor.height));
		selectionBlitMaterial.SetColor(SelectionColorShaderId, feature.SelectionColor);
		selectionBlitMaterial.SetColor(OutlineShaderId, feature.OutlineColor);

		// 指定 Render Target 和渲染前 RenderTexture 的清空方式
		// 这里需要注意的是，不能只使用颜色 RenderTexture 而不使用深度 RenderTexture，否则会报错
		ConfigureTarget(selection, selectionDepth);
		ConfigureClear(ClearFlag.All, new Color(0, 0, 0, 0));
	}

	public override void Execute(ScriptableRenderContext context, ref RenderingData renderingData)
	{
		// 在编辑器中不渲染描边
		if (renderingData.cameraData.cameraType != CameraType.Game)
		{
			return;
		}
		// 分配渲染用的 CommandBuffer
		CommandBuffer cmd = CommandBufferPool.Get(name: "Selection");
		// ProfilingScope 仅仅是为了方便在 Profiler 中显示
		using (new ProfilingScope(profilingSampler))
		{
			// 指定 DrawingSettings，这里使用了 URP 默认的 Shader Pass
			DrawingSettings drawingSettings = CreateDrawingSettings(shaderTagIdList, ref renderingData, renderingData.cameraData.defaultOpaqueSortFlags);
			RendererListParams rendererListParams = new RendererListParams(
				renderingData.cullResults,
				drawingSettings,
				// 指定 FilteringSettings，这里我们只关心渲染层
				new FilteringSettings(RenderQueueRange.all, -1, feature.RenderingLayerMask)
			);

			// 构建 RendererList
			RendererList rendererList = context.CreateRendererList(ref rendererListParams);

			// 渲染 RendererList
			cmd.DrawRendererList(rendererList);
			context.ExecuteCommandBuffer(cmd);
			cmd.Clear();

			// 准备绘制描边
			Blitter.BlitCameraTexture(cmd, selection, cameraColor, selectionBlitMaterial, 0);

			// 执行
			context.ExecuteCommandBuffer(cmd);
			cmd.Clear();
		}
		// 回收 CommandBuffer
		CommandBufferPool.Release(cmd);
	}
}
```

## 实现描边的 Shader

这里我们采取一种简单的方式，对每个像素周围的像素点采样，如果周围有 Mask 中的点而它本身不是 Mask 中的点那么便着色为描边。这里为了实现高亮效果，Mask 内本来也有的点也进行了另外的着色。

实现代码如下，注意其中与 `Blitter.BlitCameraTexture` 配合的部分:
```glsl
Shader "SelectionRendering/SelectionBlit"
{
    SubShader
    {
        Tags { "RenderType"="Opaque" "RenderPipeline" = "UniversalPipeline"}
        LOD 100
        ZWrite Off Cull Off
        Blend One OneMinusSrcAlpha
        Pass
        {
            Name "ColorBlitPass"

            HLSLPROGRAM
			// 从导入 URP 核心 HLSL 代码
            #include "Packages/com.unity.render-pipelines.universal/ShaderLibrary/Core.hlsl"
			// 从导入 Blitter 相关的 HLSL 代码
            #include "Packages/com.unity.render-pipelines.core/Runtime/Utilities/Blit.hlsl"

            #pragma vertex Vert
            #pragma fragment frag

            #define SAMPLE_COUNT 16

            uniform float _SampleDistance;
            uniform float4 _OutlineColor;
            uniform float4 _SelectionColor;

            half4 frag (Varyings input) : SV_Target
            {
				// 用于 VR 双眼渲染调整输入顶点信息，我们这里其实不需要
                UNITY_SETUP_STEREO_EYE_INDEX_POST_VERTEX(input);
				// 采样当前像素，_BlitTexture 材质的是在之前导入的 Blit.hlsl 中定义的
                float4 color = SAMPLE_TEXTURE2D_X(_BlitTexture, sampler_PointClamp, input.texcoord);

                if(color.a>0.1f){
					// 预乘 Alpha
                    return _SelectionColor*_SelectionColor.a;
                }

                int insideCount=0;

				// 采样周围的像素
                float2 texelSize=1.0/_BlitTextureSize;
                for(int i=0;i<SAMPLE_COUNT;i++){
                    float s;
                    float c;
                    sincos(radians(360.0f/((float)SAMPLE_COUNT)*((float)i)),s,c);
					// 这里采用的是采样一圈 16 个像素的方式
                    float2 uv=input.texcoord+float2(s,c)*texelSize*_SampleDistance;
                    float4 sampleColor = SAMPLE_TEXTURE2D_X(_BlitTexture, sampler_PointClamp, uv);
                    if(sampleColor.a>0.1f){
						// 统计在 Mask 中的像素的数量
                        insideCount+=1;
                    }
                }

                if(insideCount>=1){
					// 预乘 Alpha
                    return _OutlineColor*_OutlineColor.a;
                }
                return float4(0, 0, 0, 0);
            }
            ENDHLSL
        }
    }
}
```

最终效果如下：

{:.content-image}
![](../assets/img/content/unity-urp-outline/result.png)

注意较窄的物体描边会有一点渲染错误，但是这是可以接受的。当然，将来也可考虑追求一下更好的渲染效果。

## 一些遗憾

实际上，在渲染 Mask 的步骤，显然是使用单独的 Shader 而不是使用物体本来的材质比较好，这可以通过在 `DrawingSettings` 中指定一个自定义的 `ShaderTagId` 并实现。然而由于我不想给每个 Shader 都写一个自定义 Pass，所以我希望用 `DrawingSettings` 上的 `fallbackMaterial` 来为没有对应自定义 `ShaderTagId` 的 Pass 的 Shader 指定一个备用的材质。然而，这好象在当前版本中不工作，可能是 Bug，也可能是文档描述不清楚。希望将来可以好好用上这个特性。

## 小结

实现了这个描边效果之后，我算是了解了 Unity 这个扩展渲染管线的方式。不过在这个过程中也是体会到了 Unity 的文档是多么缺，它自带的 Frame Debugger 是多难用。希望将来 Unity 会有改善。另外，在调试的过程中也体会到 RenderDoc 是有多么好用。当然，也希望将来能有更多机会在实际项目中玩一些视觉效果，毕竟美术效果的独特性也是独立游戏的魅力的一部分。