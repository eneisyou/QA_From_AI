unity的资产如何打包成

AssetBundle  
我来帮您了解 Unity 中如何打包 AssetBundle。让我先搜索一下最新的官方文档和最佳实践。

Unity AssetBundle 打包教程 2024  
10件の結果

现在让我搜索官方文档了解最新的打包方法：

Unity BuildPipeline BuildAssetBundles official documentation  
10件の結果

根据搜索到的官方文档和最新教程,我现在可以为您提供完整的 Unity AssetBundle 打包方法。

## Unity AssetBundle 打包完整指南

Unity 资产打包成 AssetBundle 主要有两种方式:手动标记资源和通过代码定义打包内容。

### 方法一:手动标记资源后打包

1. 标记资源  
   在 Project 视图中选择要打包的资源,在 Inspector 底部可以看到 AssetBundle 标签设置:

   - 左侧下拉菜单:设置 AssetBundle 名称(如 model.ab)  
   - 右侧下拉菜单:设置变体名称(可选)  
   - 支持文件夹结构:使用 / 分隔,如 scene/level1  

2. 创建打包脚本  
   在 `Assets/Editor` 文件夹下创建脚本:

```csharp
using UnityEditor;
using System.IO;

public class CreateAssetBundles
{
    [MenuItem("Assets/Build AssetBundles")]
    static void BuildAllAssetBundles()
    {
        // 输出目录
        string assetBundleDirectory = "Assets/AssetBundles";
        
        // 如果目录不存在则创建
        if (!Directory.Exists(assetBundleDirectory))
        {
            Directory.CreateDirectory(assetBundleDirectory);
        }
        
        // 开始打包
        BuildPipeline.BuildAssetBundles(
            assetBundleDirectory,
            BuildAssetBundleOptions.None,
            BuildTarget.StandaloneWindows64  // 根据目标平台修改
        );
    }
}
```

3. 执行打包  
   点击菜单 `Assets → Build AssetBundles` 即可打包。

### 方法二:通过代码定义打包内容

这种方式不依赖 Inspector 中的标签设置,完全通过代码控制:

```csharp
using UnityEditor;
using System.IO;

public class BuildAssetBundlesWithMap
{
    [MenuItem("Assets/Build AssetBundles With Build Map")]
    static void BuildMapABs()
    {
        // 创建打包配置数组
        AssetBundleBuild[] buildMap = new AssetBundleBuild[2];
        
        // 配置第一个 Bundle
        buildMap[0].assetBundleName = "characters.ab";
        buildMap[0].assetNames = new string[] 
        { 
            "Assets/Models/Character1.prefab",
            "Assets/Models/Character2.prefab" 
        };
        
        // 配置第二个 Bundle
        buildMap[1].assetBundleName = "textures.ab";
        buildMap[1].assetNames = new string[] 
        { 
            "Assets/Textures/Texture1.png",
            "Assets/Textures/Texture2.png" 
        };
        
        string outputPath = "Assets/AssetBundles";
        Directory.CreateDirectory(outputPath);
        
        BuildPipeline.BuildAssetBundles(
            outputPath, 
            buildMap, 
            BuildAssetBundleOptions.None, 
            BuildTarget.StandaloneWindows64
        );
    }
}
```

### 重要的打包选项

`BuildAssetBundleOptions` 提供了三种主要的压缩格式选项 CSDN Unity :

```csharp
// 1. 不压缩 - 体积最大,加载最快
BuildAssetBundleOptions.UncompressedAssetBundle

// 2. LZMA 压缩(默认) - 体积最小,需要完整解压
BuildAssetBundleOptions.None

// 3. LZ4 压缩 - 平衡体积和速度,支持部分解压
BuildAssetBundleOptions.ChunkBasedCompression
```

其他常用选项:

```csharp
// 强制重新构建
BuildAssetBundleOptions.ForceRebuildAssetBundle

// 禁用 TypeTree(减小体积但失去向后兼容性)
BuildAssetBundleOptions.DisableWriteTypeTree

// 去除 Unity 版本信息
BuildAssetBundleOptions.AssetBundleStripUnityVersion
```

### 自动标记脚本示例

如果要批量自动标记资源,可以使用 AssetImporter CSDN :

```csharp
using UnityEditor;
using System.IO;

public class AutoSetAssetBundleNames
{
    [MenuItem("Tools/Auto Set AssetBundle Names")]
    static void SetAssetBundleNames()
    {
        // 指定资源文件夹
        string folderPath = "Assets/Models";
        
        // 获取所有资源文件
        string[] files = Directory.GetFiles(folderPath, "*.*", SearchOption.AllDirectories);
        
        foreach (string file in files)
        {
            // 跳过 .meta 文件
            if (file.EndsWith(".meta")) continue;
            
            // 获取资源导入器
            AssetImporter importer = AssetImporter.GetAtPath(file);
            
            if (importer != null)
            {
                // 设置 AssetBundle 名称
                string bundleName = "models.ab";
                importer.assetBundleName = bundleName;
                
                // 可选:设置变体
                // importer.assetBundleVariant = "hd";
            }
        }
        
        AssetDatabase.Refresh();
        UnityEngine.Debug.Log("AssetBundle names set successfully!");
    }
}
```

### 打包后的文件

打包完成后会生成:

- 每个 AssetBundle 文件及其对应的 `.manifest` 文件 Unity  
- 一个总的 manifest 文件(与输出文件夹同名)  
- manifest 文件包含依赖关系和哈希信息  

### 注意事项

- 依赖管理:没有明确设置 assetBundleName 的资源会被包含在所有引用它的 AssetBundle 中,导致资源重复 GitHub  
- 平台差异:不同平台需要分别打包,修改 BuildTarget 参数  
- 推荐使用 Addressables:Unity 官方推荐使用更友好的 Addressables 系统作为替代方案 Unity  
- 清理构建:发布时使用 `BuildAssetBundleOptions.ForceRebuildAssetBundle` 进行完全重新构建  

这样就可以完成 Unity 的 AssetBundle 打包了。如果您需要加载 AssetBundle 的代码示例,我可以继续提供。

右侧下拉菜单:设置变体名称(可选)

以上通过标签打包assetbundle，如何设置了变体改如何创建和载入，给我代码示例

我来为您详细说明 AssetBundle 变体（Variant）的创建和加载方法。

## AssetBundle 变体的使用

### 什么是变体（Variant）

变体允许您为同一资源创建不同版本（如高清版 HD、标清版 SD），它们具有相同的内部 ID，可以在运行时互相替换。

### 1. 设置变体标签

在 Unity Editor 中：

- 资源名称: `MyTexture.png`  
- AssetBundle 名称: `textures`  
- 变体名称: `hd`  

最终生成: `textures.hd`  

另一个资源：

- 资源名称: `MyTexture_Low.png`  
- AssetBundle 名称: `textures`  
- 变体名称: `sd`  

最终生成: `textures.sd`  

### 2. 通过代码设置变体

```csharp
using UnityEditor;
using System.IO;

public class SetAssetBundleVariants
{
    [MenuItem("Tools/Set HD Variant")]
    static void SetHDVariant()
    {
        // 设置高清资源
        string[] hdAssets = new string[]
        {
            "Assets/Textures/HD/Character_HD.png",
            "Assets/Models/HD/Building_HD.fbx"
        };
        
        foreach (string assetPath in hdAssets)
        {
            AssetImporter importer = AssetImporter.GetAtPath(assetPath);
            if (importer != null)
            {
                importer.assetBundleName = "gameresources";  // Bundle 名称
                importer.assetBundleVariant = "hd";          // 变体名称
            }
        }
        
        AssetDatabase.Refresh();
        UnityEngine.Debug.Log("HD variant set!");
    }
    
    [MenuItem("Tools/Set SD Variant")]
    static void SetSDVariant()
    {
        // 设置标清资源
        string[] sdAssets = new string[]
        {
            "Assets/Textures/SD/Character_SD.png",
            "Assets/Models/SD/Building_SD.fbx"
        };
        
        foreach (string assetPath in sdAssets)
        {
            AssetImporter importer = AssetImporter.GetAtPath(assetPath);
            if (importer != null)
            {
                importer.assetBundleName = "gameresources";  // 相同的 Bundle 名称
                importer.assetBundleVariant = "sd";          // 不同的变体名称
            }
        }
        
        AssetDatabase.Refresh();
        UnityEngine.Debug.Log("SD variant set!");
    }
}
```

### 3. 通过 AssetBundleBuild 打包变体

```csharp
using UnityEditor;
using System.IO;

public class BuildAssetBundleVariants
{
    [MenuItem("Assets/Build AssetBundle Variants")]
    static void BuildVariants()
    {
        AssetBundleBuild[] buildMap = new AssetBundleBuild[2];
        
        // HD 变体
        buildMap[0].assetBundleName = "gameresources";
        buildMap[0].assetBundleVariant = "hd";
        buildMap[0].assetNames = new string[]
        {
            "Assets/Textures/HD/Character_HD.png",
            "Assets/Models/HD/Building_HD.fbx"
        };
        
        // SD 变体
        buildMap[1].assetBundleName = "gameresources";
        buildMap[1].assetBundleVariant = "sd";
        buildMap[1].assetNames = new string[]
        {
            "Assets/Textures/SD/Character_SD.png",
            "Assets/Models/SD/Building_SD.fbx"
        };
        
        string outputPath = "Assets/AssetBundles";
        if (!Directory.Exists(outputPath))
        {
            Directory.CreateDirectory(outputPath);
        }
        
        BuildPipeline.BuildAssetBundles(
            outputPath,
            buildMap,
            BuildAssetBundleOptions.None,
            BuildTarget.StandaloneWindows64
        );
        
        UnityEngine.Debug.Log("Variants built successfully!");
    }
}
```

打包后会生成：

- `gameresources.hd`  
- `gameresources.sd`  
- 对应的 `.manifest` 文件  

### 4. 运行时加载变体

#### 方式一：根据设备性能动态选择

```csharp
using UnityEngine;
using System.Collections;
using System.IO;

public class AssetBundleVariantLoader : MonoBehaviour
{
    private string bundleBaseName = "gameresources";
    private string currentVariant;
    
    void Start()
    {
        // 根据设备性能选择变体
        currentVariant = DetermineVariant();
        StartCoroutine(LoadAssetBundleVariant());
    }
    
    string DetermineVariant()
    {
        // 根据系统内存判断
        if (SystemInfo.systemMemorySize >= 4096) // 4GB 以上
        {
            return "hd";
        }
        else
        {
            return "sd";
        }
        
        // 或者根据图形质量设置
        // if (QualitySettings.GetQualityLevel() >= 3)
        //     return "hd";
        // else
        //     return "sd";
    }
    
    IEnumerator LoadAssetBundleVariant()
    {
        // 构建完整的 Bundle 名称
        string bundleName = $"{bundleBaseName}.{currentVariant}";
        string bundlePath = Path.Combine(Application.streamingAssetsPath, bundleName);
        
        Debug.Log($"Loading variant: {bundleName}");
        
        // 加载 AssetBundle
        AssetBundleCreateRequest request = AssetBundle.LoadFromFileAsync(bundlePath);
        yield return request;
        
        AssetBundle bundle = request.assetBundle;
        
        if (bundle == null)
        {
            Debug.LogError($"Failed to load AssetBundle: {bundleName}");
            yield break;
        }
        
        // 加载资源
        AssetBundleRequest assetRequest = bundle.LoadAssetAsync<Texture2D>("Character");
        yield return assetRequest;
        
        Texture2D texture = assetRequest.asset as Texture2D;
        if (texture != null)
        {
            Debug.Log($"Loaded texture: {texture.name} from variant: {currentVariant}");
            // 使用纹理
            GetComponent<Renderer>().material.mainTexture = texture;
        }
        
        // 注意：不要立即卸载，等使用完毕后再卸载
        // bundle.Unload(false);
    }
}
```

#### 方式二：使用 Manifest 切换变体

```csharp
using UnityEngine;
using System.Collections;
using System.IO;

public class AssetBundleVariantSwitcher : MonoBehaviour
{
    private AssetBundleManifest manifest;
    private AssetBundle currentBundle;
    private string baseBundleName = "gameresources";
    
    void Start()
    {
        StartCoroutine(LoadManifestAndBundle());
    }
    
    IEnumerator LoadManifestAndBundle()
    {
        // 1. 先加载 Manifest
        string manifestPath = Path.Combine(Application.streamingAssetsPath, "AssetBundles");
        AssetBundleCreateRequest manifestRequest = AssetBundle.LoadFromFileAsync(manifestPath);
        yield return manifestRequest;
        
        AssetBundle manifestBundle = manifestRequest.assetBundle;
        if (manifestBundle == null)
        {
            Debug.LogError("Failed to load manifest bundle");
            yield break;
        }
        
        manifest = manifestBundle.LoadAsset<AssetBundleManifest>("AssetBundleManifest");
        manifestBundle.Unload(false);
        
        // 2. 加载默认变体 (HD)
        yield return StartCoroutine(LoadVariant("hd"));
    }
    
    IEnumerator LoadVariant(string variant)
    {
        // 卸载旧的 Bundle
        if (currentBundle != null)
        {
            currentBundle.Unload(true);
            currentBundle = null;
        }
        
        // 加载新变体
        string bundleName = $"{baseBundleName}.{variant}";
        string bundlePath = Path.Combine(Application.streamingAssetsPath, bundleName);
        
        Debug.Log($"Switching to variant: {variant}");
        
        AssetBundleCreateRequest request = AssetBundle.LoadFromFileAsync(bundlePath);
        yield return request;
        
        currentBundle = request.assetBundle;
        
        if (currentBundle == null)
        {
            Debug.LogError($"Failed to load variant: {variant}");
            yield break;
        }
        
        // 加载资源
        AssetBundleRequest assetRequest = currentBundle.LoadAssetAsync<GameObject>("Character");
        yield return assetRequest;
        
        GameObject prefab = assetRequest.asset as GameObject;
        if (prefab != null)
        {
            GameObject instance = Instantiate(prefab);
            Debug.Log($"Instantiated prefab from variant: {variant}");
        }
    }
    
    // 运行时切换变体的公共方法
    public void SwitchToHD()
    {
        StartCoroutine(LoadVariant("hd"));
    }
    
    public void SwitchToSD()
    {
        StartCoroutine(LoadVariant("sd"));
    }
    
    void OnDestroy()
    {
        if (currentBundle != null)
        {
            currentBundle.Unload(true);
        }
    }
}
```

#### 方式三：从远程服务器加载变体

```csharp
using UnityEngine;
using UnityEngine.Networking;
using System.Collections;

public class RemoteVariantLoader : MonoBehaviour
{
    private string serverURL = "https://yourserver.com/bundles/";
    private string bundleBaseName = "gameresources";
    
    void Start()
    {
        string variant = DetermineVariant();
        StartCoroutine(DownloadAndLoadVariant(variant));
    }
    
    string DetermineVariant()
    {
        // 根据网络状态选择
        if (Application.internetReachability == NetworkReachability.ReachableViaCarrierDataNetwork)
        {
            return "sd"; // 移动网络使用低质量
        }
        else
        {
            return "hd"; // WiFi 使用高质量
        }
    }
    
    IEnumerator DownloadAndLoadVariant(string variant)
    {
        string bundleName = $"{bundleBaseName}.{variant}";
        string url = serverURL + bundleName;
        
        Debug.Log($"Downloading variant from: {url}");
        
        UnityWebRequest request = UnityWebRequestAssetBundle.GetAssetBundle(url);
        yield return request.SendWebRequest();
        
        if (request.result != UnityWebRequest.Result.Success)
        {
            Debug.LogError($"Download failed: {request.error}");
            yield break;
        }
        
        AssetBundle bundle = DownloadHandlerAssetBundle.GetContent(request);
        
        if (bundle == null)
        {
            Debug.LogError("Failed to load bundle from download");
            yield break;
        }
        
        // 加载资源
        string[] allAssetNames = bundle.GetAllAssetNames();
        Debug.Log($"Loaded {allAssetNames.Length} assets from variant: {variant}");
        
        // 使用资源...
        
        bundle.Unload(false);
    }
}
```

### 5. 完整示例：带 UI 的变体切换器

```csharp
using UnityEngine;
using UnityEngine.UI;
using System.Collections;
using System.IO;

public class VariantSwitcherUI : MonoBehaviour
{
    public Button hdButton;
    public Button sdButton;
    public Text statusText;
    public GameObject displayObject;
    
    private AssetBundle currentBundle;
    private string bundleBaseName = "gameresources";
    
    void Start()
    {
        hdButton.onClick.AddListener(() => StartCoroutine(SwitchVariant("hd")));
        sdButton.onClick.AddListener(() => StartCoroutine(SwitchVariant("sd")));
        
        // 默认加载 SD 变体
        StartCoroutine(SwitchVariant("sd"));
    }
    
    IEnumerator SwitchVariant(string variant)
    {
        statusText.text = $"正在加载 {variant.ToUpper()} 版本...";
        hdButton.interactable = false;
        sdButton.interactable = false;
        
        // 卸载旧资源
        if (currentBundle != null)
        {
            currentBundle.Unload(true);
            currentBundle = null;
        }
        
        // 清空显示对象
        if (displayObject != null)
        {
            Destroy(displayObject);
        }
        
        // 加载新变体
        string bundlePath = Path.Combine(
            Application.streamingAssetsPath, 
            $"{bundleBaseName}.{variant}"
        );
        
        AssetBundleCreateRequest request = AssetBundle.LoadFromFileAsync(bundlePath);
        yield return request;
        
        currentBundle = request.assetBundle;
        
        if (currentBundle == null)
        {
            statusText.text = $"加载失败: {variant}";
            hdButton.interactable = true;
            sdButton.interactable = true;
            yield break;
        }
        
        // 加载预制体
        AssetBundleRequest assetRequest = currentBundle.LoadAssetAsync<GameObject>("Character");
        yield return assetRequest;
        
        GameObject prefab = assetRequest.asset as GameObject;
        if (prefab != null)
        {
            displayObject = Instantiate(prefab);
            displayObject.transform.position = Vector3.zero;
            statusText.text = $"已加载 {variant.ToUpper()} 版本";
        }
        else
        {
            statusText.text = "加载资源失败";
        }
        
        hdButton.interactable = true;
        sdButton.interactable = true;
    }
    
    void OnDestroy()
    {
        if (currentBundle != null)
        {
            currentBundle.Unload(true);
        }
    }
}
```

### 关键要点

- 变体的内部 ID 相同：HD 和 SD 版本的资源具有相同的内部标识，可以无缝切换  
- 文件命名规则：`bundlename.variant`（如 `textures.hd`、`textures.sd`）  
- 只能加载一个变体：同一时间只能加载同一 Bundle 的一个变体  
- 切换时需要卸载：切换变体前必须先卸载当前变体  
- 资源名称要一致：HD 和 SD 版本的资源在 Bundle 中的名称应该相同  

这样您就可以根据设备性能、网络状况或用户设置动态加载不同质量的资源了！

打包后会生成：

* gameresources.hd  
* gameresources.sd  

assetbundle不是以.ab结尾吗？

您说得对！我需要澄清这个重要的概念。

## AssetBundle 文件扩展名说明

### AssetBundle 的扩展名是灵活的

AssetBundle 没有强制的文件扩展名要求，`.ab` 只是一个常用的约定，但不是必须的。

实际上：

#### 扩展名由你决定：

```csharp
   // 可以是 .ab
   importer.assetBundleName = "textures.ab";
   
   // 可以是 .unity3d
   importer.assetBundleName = "textures.unity3d";
   
   // 可以没有扩展名
   importer.assetBundleName = "textures";
   
   // 甚至可以是自定义的
   importer.assetBundleName = "textures.bundle";
```

#### 变体情况下的命名规则：

- Bundle 名称 + `.` + 变体名称  
- 最终文件名 = `bundlename.variant`  

### 正确的变体命名示例

如果您想要 `.ab` 扩展名，应该这样设置：

#### 方式一：Bundle 名称包含 .ab

```csharp
// HD 变体
importer.assetBundleName = "gameresources.ab";  // Bundle 名称
importer.assetBundleVariant = "hd";              // 变体名称

// 最终生成文件: gameresources.ab.hd
```

```csharp
// SD 变体
importer.assetBundleName = "gameresources.ab";  // 相同的 Bundle 名称
importer.assetBundleVariant = "sd";              // 变体名称

// 最终生成文件: gameresources.ab.sd
```

打包后生成：

- `gameresources.ab.hd`  
- `gameresources.ab.sd`  

#### 方式二：变体名称包含 .ab（不推荐）

```csharp
// HD 变体
importer.assetBundleName = "gameresources";
importer.assetBundleVariant = "hd.ab";

// 最终生成文件: gameresources.hd.ab
```

### 完整的代码示例（带 .ab 扩展名）

```csharp
using UnityEditor;
using System.IO;

public class BuildAssetBundleVariantsWithAB
{
    [MenuItem("Assets/Build AssetBundle Variants (.ab)")]
    static void BuildVariants()
    {
        AssetBundleBuild[] buildMap = new AssetBundleBuild[2];
        
        // HD 变体 - 注意 Bundle 名称包含 .ab
        buildMap[0].assetBundleName = "gameresources.ab";
        buildMap[0].assetBundleVariant = "hd";
        buildMap[0].assetNames = new string[]
        {
            "Assets/Textures/HD/Character_HD.png",
            "Assets/Models/HD/Building_HD.fbx"
        };
        
        // SD 变体
        buildMap[1].assetBundleName = "gameresources.ab";
        buildMap[1].assetBundleVariant = "sd";
        buildMap[1].assetNames = new string[]
        {
            "Assets/Textures/SD/Character_SD.png",
            "Assets/Models/SD/Building_SD.fbx"
        };
        
        string outputPath = "Assets/AssetBundles";
        if (!Directory.Exists(outputPath))
        {
            Directory.CreateDirectory(outputPath);
        }
        
        BuildPipeline.BuildAssetBundles(
            outputPath,
            buildMap,
            BuildAssetBundleOptions.None,
            BuildTarget.StandaloneWindows64
        );
        
        UnityEngine.Debug.Log("Variants built successfully!");
        // 生成文件: gameresources.ab.hd 和 gameresources.ab.sd
    }
}
```

### 加载时的对应修改

```csharp
using UnityEngine;
using System.Collections;
using System.IO;

public class LoadAssetBundleVariantWithAB : MonoBehaviour
{
    private string bundleBaseName = "gameresources.ab";  // 注意这里包含 .ab
    
    void Start()
    {
        string variant = DetermineVariant();
        StartCoroutine(LoadVariant(variant));
    }
    
    string DetermineVariant()
    {
        return SystemInfo.systemMemorySize >= 4096 ? "hd" : "sd";
    }
    
    IEnumerator LoadVariant(string variant)
    {
        // 构建完整文件名: gameresources.ab.hd 或 gameresources.ab.sd
        string bundleName = $"{bundleBaseName}.{variant}";
        string bundlePath = Path.Combine(Application.streamingAssetsPath, bundleName);
        
        Debug.Log($"Loading: {bundleName}");
        
        AssetBundleCreateRequest request = AssetBundle.LoadFromFileAsync(bundlePath);
        yield return request;
        
        AssetBundle bundle = request.assetBundle;
        
        if (bundle == null)
        {
            Debug.LogError($"Failed to load: {bundleName}");
            yield break;
        }
        
        // 加载资源
        AssetBundleRequest assetRequest = bundle.LoadAssetAsync<Texture2D>("Character");
        yield return assetRequest;
        
        Texture2D texture = assetRequest.asset as Texture2D;
        if (texture != null)
        {
            Debug.Log($"Loaded from: {bundleName}");
            GetComponent<Renderer>().material.mainTexture = texture;
        }
    }
}
```

### 常见的命名约定

在实际项目中，您会看到不同的命名风格：

```csharp
// 风格 1: 使用 .ab 扩展名
"characters.ab" + "hd" → characters.ab.hd
"characters.ab" + "sd" → characters.ab.sd

// 风格 2: 使用 .bundle 扩展名
"characters.bundle" + "hd" → characters.bundle.hd
"characters.bundle" + "sd" → characters.bundle.sd

// 风格 3: 无扩展名
"characters" + "hd" → characters.hd
"characters" + "sd" → characters.sd

// 风格 4: 变体包含扩展名（较少见）
"characters" + "hd.ab" → characters.hd.ab
"characters" + "sd.ab" → characters.sd.ab
```

### 推荐做法

```csharp
public class AssetBundleNamingStandard
{
    // 推荐：Bundle 名称包含 .ab，变体名称不含扩展名
    private const string BUNDLE_EXTENSION = ".ab";
    
    public static void SetBundleWithVariant(string assetPath, string bundleName, string variant)
    {
        AssetImporter importer = AssetImporter.GetAtPath(assetPath);
        if (importer != null)
        {
            // Bundle 名称: "characters.ab"
            importer.assetBundleName = bundleName + BUNDLE_EXTENSION;
            // 变体名称: "hd" 或 "sd"
            importer.assetBundleVariant = variant;
            // 最终文件: "characters.ab.hd" 或 "characters.ab.sd"
        }
    }
}
```

### 总结

✅ AssetBundle 可以使用任何扩展名或不使用扩展名  
✅ `.ab` 只是常用约定，不是强制要求  
✅ 变体的完整文件名格式：`bundlename.variant`  
✅ 如果想要 `.ab` 结尾，应该在 Bundle 名称中包含 `.ab`  
✅ 最终生成：`gameresources.ab.hd` 和 `gameresources.ab.sd`  

感谢您的纠正，这个细节确实很重要！

把多个资产打包到一个assetbundle里，实例化的时候如何选择实例化哪个资产

好问题！当一个 AssetBundle 包含多个资产时，需要通过资产名称来指定加载哪个资产。让我给您详细的示例。

## 加载 AssetBundle 中的特定资产

### 方法一：通过资产名称加载

```csharp
using UnityEngine;
using System.Collections;
using System.IO;

public class LoadSpecificAsset : MonoBehaviour
{
    void Start()
    {
        StartCoroutine(LoadAndInstantiateSpecificAsset());
    }
    
    IEnumerator LoadAndInstantiateSpecificAsset()
    {
        // 1. 加载 AssetBundle
        string bundlePath = Path.Combine(Application.streamingAssetsPath, "characters.ab");
        AssetBundleCreateRequest bundleRequest = AssetBundle.LoadFromFileAsync(bundlePath);
        yield return bundleRequest;
        
        AssetBundle bundle = bundleRequest.assetBundle;
        if (bundle == null)
        {
            Debug.LogError("Failed to load AssetBundle");
            yield break;
        }
        
        // 2. 通过名称加载特定资产（不含路径和扩展名）
        // 假设 Bundle 里有: Character1.prefab, Character2.prefab, Character3.prefab
        
        // 加载 Character1
        AssetBundleRequest assetRequest1 = bundle.LoadAssetAsync<GameObject>("Character1");
        yield return assetRequest1;
        GameObject prefab1 = assetRequest1.asset as GameObject;
        if (prefab1 != null)
        {
            Instantiate(prefab1, new Vector3(0, 0, 0), Quaternion.identity);
        }
        
        // 加载 Character2
        AssetBundleRequest assetRequest2 = bundle.LoadAssetAsync<GameObject>("Character2");
        yield return assetRequest2;
        GameObject prefab2 = assetRequest2.asset as GameObject;
        if (prefab2 != null)
        {
            Instantiate(prefab2, new Vector3(3, 0, 0), Quaternion.identity);
        }
        
        // 加载 Character3
        AssetBundleRequest assetRequest3 = bundle.LoadAssetAsync<GameObject>("Character3");
        yield return assetRequest3;
        GameObject prefab3 = assetRequest3.asset as GameObject;
        if (prefab3 != null)
        {
            Instantiate(prefab3, new Vector3(6, 0, 0), Quaternion.identity);
        }
        
        // 3. 不要忘记卸载（使用完后）
        // bundle.Unload(false); // false: 只卸载 Bundle，不卸载已加载的资产
    }
}
```

### 方法二：同步加载（适用于已缓存的资源）

```csharp
using UnityEngine;
using System.IO;

public class LoadAssetSync : MonoBehaviour
{
    void Start()
    {
        // 1. 同步加载 AssetBundle
        string bundlePath = Path.Combine(Application.streamingAssetsPath, "characters.ab");
        AssetBundle bundle = AssetBundle.LoadFromFile(bundlePath);
        
        if (bundle == null)
        {
            Debug.LogError("Failed to load AssetBundle");
            return;
        }
        
        // 2. 同步加载特定资产
        GameObject prefab1 = bundle.LoadAsset<GameObject>("Character1");
        GameObject prefab2 = bundle.LoadAsset<GameObject>("Character2");
        GameObject prefab3 = bundle.LoadAsset<GameObject>("Character3");
        
        // 3. 实例化
        if (prefab1 != null) Instantiate(prefab1, new Vector3(0, 0, 0), Quaternion.identity);
        if (prefab2 != null) Instantiate(prefab2, new Vector3(3, 0, 0), Quaternion.identity);
        if (prefab3 != null) Instantiate(prefab3, new Vector3(6, 0, 0), Quaternion.identity);
        
        // 4. 卸载 Bundle（资产已加载到内存）
        bundle.Unload(false);
    }
}
```

### 方法三：查看 Bundle 中所有资产并选择性加载

```csharp
using UnityEngine;
using System.Collections;
using System.IO;

public class LoadAssetByNameFromList : MonoBehaviour
{
    void Start()
    {
        StartCoroutine(LoadAndSelectAsset());
    }
    
    IEnumerator LoadAndSelectAsset()
    {
        // 1. 加载 AssetBundle
        string bundlePath = Path.Combine(Application.streamingAssetsPath, "characters.ab");
        AssetBundleCreateRequest bundleRequest = AssetBundle.LoadFromFileAsync(bundlePath);
        yield return bundleRequest;
        
        AssetBundle bundle = bundleRequest.assetBundle;
        if (bundle == null)
        {
            Debug.LogError("Failed to load AssetBundle");
            yield break;
        }
        
        // 2. 获取所有资产名称
        string[] assetNames = bundle.GetAllAssetNames();
        Debug.Log("Assets in bundle:");
        foreach (var name in assetNames)
        {
            Debug.Log(name);
        }
        
        // 3. 假设你想加载名为 "character2" 的资产（不区分大小写、去掉路径和扩展名）
        string targetName = "character2";
        string matchedAssetPath = null;
        
        foreach (var fullName in assetNames)
        {
            // 示例: fullName 可能是 "assets/characters/character2.prefab"
            string fileName = Path.GetFileNameWithoutExtension(fullName); // character2
            if (string.Equals(fileName, targetName, System.StringComparison.OrdinalIgnoreCase))
            {
                matchedAssetPath = fullName;
                break;
            }
        }
        
        if (matchedAssetPath == null)
        {
            Debug.LogError($"Asset with name {targetName} not found in bundle");
            yield break;
        }
        
        // 4. 加载匹配到的资产
        AssetBundleRequest assetRequest = bundle.LoadAssetAsync<GameObject>(matchedAssetPath);
        yield return assetRequest;
        
        GameObject prefab = assetRequest.asset as GameObject;
        if (prefab != null)
        {
            Instantiate(prefab, Vector3.zero, Quaternion.identity);
        }
        
        // 5. 使用完后卸载（根据需要）
        // bundle.Unload(false);
    }
}
```

### 方法四：结合配置表（推荐做法）

```csharp
using UnityEngine;
using System.Collections;
using System.Collections.Generic;
using System.IO;

[System.Serializable]
public class CharacterConfig
{
    public string id;         // 角色 ID，例如 "hero_001"
    public string assetName;  // 对应的 prefab 名称，例如 "HeroKnight"
}

[System.Serializable]
public class CharacterConfigList
{
    public List<CharacterConfig> characters;
}

public class ConfigDrivenAssetLoader : MonoBehaviour
{
    public TextAsset characterConfigJson; // 在 Inspector 里指定 JSON 配置
    private Dictionary<string, string> idToAssetName;
    
    void Awake()
    {
        // 解析配置
        var list = JsonUtility.FromJson<CharacterConfigList>(characterConfigJson.text);
        idToAssetName = new Dictionary<string, string>();
        foreach (var cfg in list.characters)
        {
            idToAssetName[cfg.id] = cfg.assetName;
        }
    }
    
    void Start()
    {
        // 示例：根据角色 ID 加载不同角色
        StartCoroutine(LoadCharacterById("hero_001"));
    }
    
    IEnumerator LoadCharacterById(string characterId)
    {
        if (!idToAssetName.TryGetValue(characterId, out string assetName))
        {
            Debug.LogError($"No asset mapped for character id: {characterId}");
            yield break;
        }
        
        string bundlePath = Path.Combine(Application.streamingAssetsPath, "characters.ab");
        AssetBundleCreateRequest bundleRequest = AssetBundle.LoadFromFileAsync(bundlePath);
        yield return bundleRequest;
        
        AssetBundle bundle = bundleRequest.assetBundle;
        if (bundle == null)
        {
            Debug.LogError("Failed to load AssetBundle");
            yield break;
        }
        
        AssetBundleRequest assetRequest = bundle.LoadAssetAsync<GameObject>(assetName);
        yield return assetRequest;
        
        GameObject prefab = assetRequest.asset as GameObject;
        if (prefab != null)
        {
            Instantiate(prefab, Vector3.zero, Quaternion.identity);
        }
        
        // bundle.Unload(false);
    }
}
```

### 要点总结

- 一个 AssetBundle 可以包含多个资产（Prefab、Texture、Audio 等）  
- 加载时通过**资产名称**来区分要实例化哪个  
- `LoadAsset`/`LoadAssetAsync` 可以按名称或完整路径加载  
- 可以先用 `GetAllAssetNames()` 列出所有资产，再做筛选  
- 推荐用“配置表 + 资产名”的方式来管理和选择要实例化的对象  

如果你希望，我也可以在此基础上继续整理一个更「教程化」的 README 结构，但会严格保持你现有文字不增删，只是重新分章、加目录。  
