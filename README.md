
using UnityEngine;
using System.Collections;
using UnityEditor;
using System.IO;
using System;

public class AutoSetTextureUISprite : AssetPostprocessor
{
    public static bool canSet = false;
    public static ImportTextureSetting importSetting = new ImportTextureSetting();
    void OnPreprocessTexture()
    {
        if (canSet)
        {
            TextureImporter textureIr = (TextureImporter)assetImporter;
            //自动设置类型;
            ResetVaria1ble(textureIr, importSetting);
        }
    }
    static void OnPostprocessAllAssets(string[] importedAssets, string[] deletedAssets, string[] movedAssets, string[] movedFromAssetPaths)
    {
        canSet = false;
        if (SetWindow.window != null)
            SetWindow.window.Close();
    }


    /// <summary>重置变量</summary>
    /// <param name="textureImporter">图片导入器</param>
    private void ResetVaria1ble(TextureImporter textureImporter, ImportTextureSetting importSetting)
    {
        textureImporter.textureType = importSetting.texType;
        textureImporter.textureShape = importSetting.texShape;
        textureImporter.sRGBTexture = importSetting.sRGBTexture;
        textureImporter.alphaSource = importSetting.alphaSource;
        textureImporter.alphaIsTransparency = importSetting.alphaIsTransparency;
        textureImporter.spritePackingTag = importSetting.spritePackingTag;
        textureImporter.spriteImportMode = importSetting.spriteMode;
        textureImporter.npotScale = importSetting.NPO2;
        textureImporter.isReadable = importSetting.isReadable;
        textureImporter.mipmapEnabled = importSetting.mipmapEnabled;
        textureImporter.mipmapFilter = importSetting.mipFilter;
        textureImporter.fadeout = importSetting.fadeoutMipmaps;
        textureImporter.wrapMode = importSetting.wrapMode;
        textureImporter.filterMode = importSetting.filterMode;
        textureImporter.anisoLevel = importSetting.anisoLv;
        textureImporter.maxTextureSize = importSetting.maxSize;
        textureImporter.textureCompression = importSetting.compre;
        //textureImporter.textureFormat = TextureImporterFormat.Automatic;
        textureImporter.crunchedCompression = importSetting.crunchedCompression;
        textureImporter.compressionQuality = importSetting.compressionQuality;
    }
}
/// <summary>
/// 设置窗口
/// </summary>
public class SetWindow : EditorWindow
{
    /// <summary>
    /// 窗口实例
    /// </summary>
    public static SetWindow window;
    /// <summary>
    /// 图片导入设置
    /// </summary>
    private ImportTextureSetting its;
    /// <summary>
    /// 导入图片尺寸
    /// </summary>
    private Size size = Size.Size_2048;
    private bool SelectFile = false;
    private bool overWrite = false;
    /// <summary>
    /// 文件夹路径
    /// </summary>
    private string folderPath = "";
    /// <summary>
    /// 文件路径
    /// </summary>
    private string filePath = "";
    private string ToPath = "";

    [MenuItem("MyTools/SetTextureImportFormat #%&q")]
    public static void SetTextureImport()
    {
        window = (SetWindow)GetWindow(typeof(SetWindow), true, "设置图片导入格式", true);
        window.its = new ImportTextureSetting();
        window.Show();
    }
    public void CloseWindow()
    {
        if (window != null)
        {
            if (window.its != null) window.its = null;
            if (window) window = null;
        }
        if (window != null) Close();
    }
    private void OnDestroy()
    {
        CloseWindow();
    }
    private void OnGUI()
    {
        GUILayout.Space(20);
        window.its.texType = (TextureImporterType)EditorGUILayout.EnumPopup("Texture Type", window.its.texType);
        if (window.its.texType == TextureImporterType.Sprite)
        {
            window.its.spritePackingTag = EditorGUILayout.TextField("spritePackingTag", window.its.spritePackingTag);
            window.its.spriteMode = (SpriteImportMode)EditorGUILayout.EnumPopup("Sprite Mode", window.its.spriteMode);
        }
        window.its.texShape = (TextureImporterShape)EditorGUILayout.EnumPopup("Texture Shape", window.its.texShape);
        window.its.sRGBTexture = EditorGUILayout.Toggle("sRGBTexture", window.its.sRGBTexture);
        window.its.alphaSource = (TextureImporterAlphaSource)EditorGUILayout.EnumPopup("Texture Shape", window.its.alphaSource);
        window.its.alphaIsTransparency = EditorGUILayout.Toggle("alphaIsTransparency", window.its.alphaIsTransparency);
        window.its.NPO2 = (TextureImporterNPOTScale)EditorGUILayout.EnumPopup("Non Power of 2", window.its.NPO2);
        window.its.isReadable = EditorGUILayout.Toggle("Read/Write Enable", window.its.isReadable);
        window.its.mipmapEnabled = EditorGUILayout.Toggle("mipmapEnabled", window.its.mipmapEnabled);
        if (window.its.mipmapEnabled)
        {
            window.its.borderMipmap = EditorGUILayout.Toggle("borderMipmap", window.its.borderMipmap);
            window.its.mipFilter = (TextureImporterMipFilter)EditorGUILayout.EnumPopup("mipFilter", window.its.mipFilter);
            window.its.fadeoutMipmaps = EditorGUILayout.Toggle("fadeoutMipmaps", window.its.fadeoutMipmaps);
        }
        window.its.wrapMode = (TextureWrapMode)EditorGUILayout.EnumPopup("WarpMode", window.its.wrapMode);
        window.its.filterMode = (FilterMode)EditorGUILayout.EnumPopup("filterMode", window.its.filterMode);
        window.its.anisoLv = EditorGUILayout.IntSlider("Aniso Level", window.its.anisoLv, 0, 16);
        window.its.maxSize = (int)(Size)EditorGUILayout.EnumPopup("Max Size", size);
        window.its.compre = (TextureImporterCompression)EditorGUILayout.EnumPopup(window.its.compre);
        window.its.crunchedCompression = EditorGUILayout.Toggle("crunched Compression", window.its.crunchedCompression);
        if (window.its.crunchedCompression)
            window.its.compressionQuality = EditorGUILayout.IntSlider("Compression Quality", window.its.compressionQuality, 0, 100);
        overWrite = EditorGUILayout.Toggle("Over Write", overWrite);
        if (SelectFile)
        {
            folderPath = "";
            if (GUILayout.Button("导入文件夹"))
            {
                SelectFile = false;
                ToPath = "";
            }
            GUILayout.Label("File Path : ");
            filePath = GUILayout.TextField(filePath);
            if (GUILayout.Button("浏览..."))
                filePath = EditorUtility.OpenFilePanelWithFilters("选择文件...", Application.dataPath, new string[] { "png", "jpg" });
            GUILayout.Label("To Path");
            ToPath = GUILayout.TextField(ToPath);
            if (GUILayout.Button("To..."))
            {
                ToPath = EditorUtility.OpenFolderPanel("选择文件夹...", Application.dataPath, "");
            }
        }
        if (!SelectFile)
        {
            filePath = "";
            if (GUILayout.Button("导入文件"))
            {
                SelectFile = true;
                ToPath = "";
            }
            GUILayout.Label("Folder Path : ");
            folderPath = GUILayout.TextField(folderPath);
            if (GUILayout.Button("浏览..."))
                folderPath = EditorUtility.OpenFolderPanel("选择文件夹...", Application.dataPath, "");
            GUILayout.Label("To Path");
            ToPath = GUILayout.TextField(ToPath);
            if (GUILayout.Button("To..."))
                ToPath = EditorUtility.OpenFolderPanel("选择文件夹...", Application.dataPath, "");
        }
        if (GUILayout.Button("设置"))
        {
            SetInfo(window.its);
            AutoSetTextureUISprite.canSet = true;
            window.Close();
            ToPath = (string.IsNullOrEmpty(ToPath) ? Application.dataPath : ToPath).Replace("\\", "/");
            ToPath = ToPath.EndsWith("/") ? ToPath : ToPath + "/";
            //if (!Directory.Exists(ToPath)) Directory.CreateDirectory(ToPath);
            if (!Directory.Exists(ToPath))
            {
                AssetDatabase.CreateFolder("Assets", ToPath.Replace(Application.dataPath + "/", ""));
            }
            if (!string.IsNullOrEmpty(filePath) && File.Exists(filePath))
            {
                File.Copy(filePath, ToPath + Path.GetFileName(filePath), overWrite);
            }
            if (!string.IsNullOrEmpty(folderPath) && Directory.Exists(folderPath))
            {
                System.Collections.Generic.List<string> files = new System.Collections.Generic.List<string>();
                System.Collections.Generic.List<string> Dires = new System.Collections.Generic.List<string>();
                files.AddRange(Directory.GetFiles(folderPath));
                Dires.AddRange(Directory.GetDirectories(folderPath));
                int index = 0;
                while (index < Dires.Count)
                {
                    Dires.AddRange(Directory.GetDirectories(Dires[index]));
                    files.AddRange(Directory.GetFiles(Dires[index++]));
                }
                Dires.Clear();
                Dires = null;
                if (ToPath == Application.dataPath + "/")
                    ToPath += Path.GetFileName(folderPath) + "/";
                if (overWrite && Directory.Exists(ToPath))
                {
                    Directory.Delete(ToPath, true);
                    File.Delete(ToPath.Substring(0, ToPath.Length - 1) + ".meta");
                    AssetDatabase.Refresh();
                    AutoSetTextureUISprite.canSet = true;
                }
                if (!Directory.Exists(ToPath)) Directory.CreateDirectory(ToPath);
                foreach (string file in files)
                {
                    File.Copy(file, ToPath + Path.GetFileName(file), overWrite);
                }
                files.Clear();
                files = null;
                System.GC.Collect();
            }
            ToPath = "";
            AssetDatabase.Refresh();
        }
        if (GUILayout.Button("取消"))
        {
            AutoSetTextureUISprite.canSet = false;
            CloseWindow();
        }
    }

    private void SetInfo(ImportTextureSetting its)
    {
        //AutoSetTextureUISprite.importSetting = its;
        System.Reflection.FieldInfo[] fields = its.GetType().GetFields(System.Reflection.BindingFlags.Instance | System.Reflection.BindingFlags.Public | System.Reflection.BindingFlags.DeclaredOnly);
        foreach (System.Reflection.FieldInfo field in fields)
        {
            field.SetValue(AutoSetTextureUISprite.importSetting, field.GetValue(its));
        }
    }
}
public enum Size
{
    Size_32 = 32,
    Size_64 = 64,
    Size_128 = 128,
    Size_256 = 256,
    Size_512 = 512,
    Size_1024 = 1024,
    Size_2048 = 2048,
    Size_4096 = 4096,
    Size_8192 = 8192
}
public class ImportTextureSetting
{
    public TextureImporterType texType = TextureImporterType.Default;
    public TextureImporterShape texShape = TextureImporterShape.Texture2D;
    public bool sRGBTexture = true;
    public TextureImporterAlphaSource alphaSource = TextureImporterAlphaSource.None;
    public bool alphaIsTransparency = false;
    public string spritePackingTag = "";
    public SpriteImportMode spriteMode = SpriteImportMode.Single;
    public TextureImporterNPOTScale NPO2 = TextureImporterNPOTScale.ToNearest;
    public bool isReadable = false;
    public bool mipmapEnabled = false;
    public bool borderMipmap = false;
    public TextureImporterMipFilter mipFilter = TextureImporterMipFilter.BoxFilter;
    public bool fadeoutMipmaps = false;
    public TextureWrapMode wrapMode = TextureWrapMode.Repeat;
    public FilterMode filterMode = FilterMode.Bilinear;
    public int anisoLv = 1;
    public int maxSize = 2048;
    public TextureImporterCompression compre = TextureImporterCompression.Compressed;
    //public TextureImporterFormat texFormat = TextureImporterFormat.Automatic;
    public bool crunchedCompression = false;
    public int compressionQuality = 50;
}
