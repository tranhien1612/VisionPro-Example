# Step

## Image Source

Click ``` Choose File```, chọn file ảnh vật thể cần đo khoảnh cách.

## CogPMAlignTool

Chọn Current.TrainImage ở list (góc trên bên phải), sau đó kéo để khoanh vùng vật thể cần detect sau đó nhấn ``` Grab Train Image ``` và ``` Train ```.

Sang tab ``` Train Region & Origin ``` chọn ``` Center Origin ``` để đặt gốc trụ tọa độ vào tâm của vật thể.

Tab ``` Graphic ```, tích chọn ``` Show Fine ```

## CogFixtureTool

Dùng để chuyển đổi hệ tọa độ

## CogCaliperTool

Khoanh vùng cần đo khoảng cách.

Xác định xem 2 cạnh là dạng ``` Dark to Light ``` hay ``` Light to Dark ``` (2 cạnh này là 2 cạnh dùng để đo khoảng cách, thuật toán sẽ xác định vị trí chuyển màu từ Dark->Light or Light->Dark là vị trí của cạnh).

Thay đổi giá trị ``` Edge Pair Width ``` và nhìn xem 2 cạnh nó khớp vào vị trí cần đo chưa.


## SizeMeasurementAdvance.vpp

Thêm script để hiển thị thông tin lên trên hình ảnh

Sử dụng CogToolBlock để chứa các tool xử lý. Script C# Advance in ```CogToolBlock```.
```
#region namespace imports
using System;
using System.Collections;
using System.Drawing;
using System.IO;
using System.Windows.Forms;
using Cognex.VisionPro;
using Cognex.VisionPro.ToolBlock;
using Cognex.VisionPro3D;
using Cognex.VisionPro.PMAlign;
using Cognex.VisionPro.CalibFix;
using Cognex.VisionPro.Caliper;
#endregion

public class CogToolBlockAdvancedScript : CogToolBlockAdvancedScriptBase
{
  #region Private Member Variables
  private Cognex.VisionPro.ToolBlock.CogToolBlock mToolBlock;
  
  CogGraphicLabel myLabel = new CogGraphicLabel();
  #endregion

  /// <summary>
  /// Called when the parent tool is run.
  /// Add code here to customize or replace the normal run behavior.
  /// </summary>
  /// <param name="message">Sets the Message in the tool's RunStatus.</param>
  /// <param name="result">Sets the Result in the tool's RunStatus</param>
  /// <returns>True if the tool should run normally,
  ///          False if GroupRun customizes run behavior</returns>
  public override bool GroupRun(ref string message, ref CogToolResultConstants result)
  {
    // To let the execution stop in this script when a debugger is attached, uncomment the following lines.
    // #if DEBUG
    // if (System.Diagnostics.Debugger.IsAttached) System.Diagnostics.Debugger.Break();
    // #endif

    // Run each tool using the RunTool function
    foreach(ICogTool tool in mToolBlock.Tools)
      mToolBlock.RunTool(tool, ref message, ref result);
    
    CogCaliperTool caliper = mToolBlock.Tools["CogCaliperTool1"] as CogCaliperTool;
    myLabel.SetXYText(0, -20, "Len: " + caliper.Results[0].Width.ToString("F3"));
    myLabel.Color = CogColorConstants.Blue;
    myLabel.Font = new Font("Cambrial", 20);
    
    return false;
  }

  #region When the Current Run Record is Created
  /// <summary>
  /// Called when the current record may have changed and is being reconstructed
  /// </summary>
  /// <param name="currentRecord">
  /// The new currentRecord is available to be initialized or customized.</param>
  public override void ModifyCurrentRunRecord(Cognex.VisionPro.ICogRecord currentRecord)
  {
  }
  #endregion

  #region When the Last Run Record is Created
  /// <summary>
  /// Called when the last run record may have changed and is being reconstructed
  /// </summary>
  /// <param name="lastRecord">
  /// The new last run record is available to be initialized or customized.</param>
  public override void ModifyLastRunRecord(Cognex.VisionPro.ICogRecord lastRecord)
  {
    mToolBlock.AddGraphicToRunRecord(myLabel, lastRecord, "CogFixtureTool1.OutputImage", "");
  }
  #endregion

  #region When the Script is Initialized
  /// <summary>
  /// Perform any initialization required by your script here
  /// </summary>
  /// <param name="host">The host tool</param>
  public override void Initialize(Cognex.VisionPro.ToolGroup.CogToolGroup host)
  {
    // DO NOT REMOVE - Call the base class implementation first - DO NOT REMOVE
    base.Initialize(host);

    // Store a local copy of the script host
    this.mToolBlock = ((Cognex.VisionPro.ToolBlock.CogToolBlock)(host));
  }
  #endregion
}

```

## SizeMeasurementAdvance_2.vpp

Thêm tool và script để đo và hiển thị bán bán kính lên trên hình ảnh

```
#region namespace imports
using System;
using System.Collections;
using System.Drawing;
using System.IO;
using System.Windows.Forms;
using Cognex.VisionPro;
using Cognex.VisionPro.ToolBlock;
using Cognex.VisionPro3D;
using Cognex.VisionPro.PMAlign;
using Cognex.VisionPro.CalibFix;
using Cognex.VisionPro.Caliper;
#endregion

public class CogToolBlockAdvancedScript : CogToolBlockAdvancedScriptBase
{
  #region Private Member Variables
  private Cognex.VisionPro.ToolBlock.CogToolBlock mToolBlock;
  
  CogGraphicLabel myLabel = new CogGraphicLabel();
  CogGraphicLabel myLabel_1 = new CogGraphicLabel();
  CogGraphicLabel myLabel_2 = new CogGraphicLabel();
  #endregion

  /// <summary>
  /// Called when the parent tool is run.
  /// Add code here to customize or replace the normal run behavior.
  /// </summary>
  /// <param name="message">Sets the Message in the tool's RunStatus.</param>
  /// <param name="result">Sets the Result in the tool's RunStatus</param>
  /// <returns>True if the tool should run normally,
  ///          False if GroupRun customizes run behavior</returns>
  public override bool GroupRun(ref string message, ref CogToolResultConstants result)
  {
    // To let the execution stop in this script when a debugger is attached, uncomment the following lines.
    // #if DEBUG
    // if (System.Diagnostics.Debugger.IsAttached) System.Diagnostics.Debugger.Break();
    // #endif


    // Run each tool using the RunTool function
    foreach(ICogTool tool in mToolBlock.Tools)
      mToolBlock.RunTool(tool, ref message, ref result);
    
    CogCaliperTool caliper = mToolBlock.Tools["CogCaliperTool1"] as CogCaliperTool;
    myLabel.SetXYText(0, -20, "Len: " + caliper.Results[0].Width.ToString("F3"));
    myLabel.Color = CogColorConstants.Blue;
    myLabel.Font = new Font("Cambrial", 20);
    
    CogFindCircleTool findCircle1 = mToolBlock.Tools["CogFindCircleTool1"] as CogFindCircleTool;
    CogFindCircleTool findCircle2 = mToolBlock.Tools["CogFindCircleTool2"] as CogFindCircleTool;
    
    myLabel_1.SetXYText(findCircle1.Results.GetCircle().CenterX, findCircle1.Results.GetCircle().CenterY, findCircle1.Results.GetCircle().Radius.ToString("F2"));
    myLabel_1.Color = CogColorConstants.Blue;
    myLabel_1.Font = new Font("Cambrial", 20);

    myLabel_2.SetXYText(findCircle2.Results.GetCircle().CenterX, findCircle2.Results.GetCircle().CenterY, findCircle2.Results.GetCircle().Radius.ToString("F2"));
    myLabel_2.Color = CogColorConstants.Blue;
    myLabel_2.Font = new Font("Cambrial", 20);
    
    return false;
  }

  #region When the Current Run Record is Created
  /// <summary>
  /// Called when the current record may have changed and is being reconstructed
  /// </summary>
  /// <param name="currentRecord">
  /// The new currentRecord is available to be initialized or customized.</param>
  public override void ModifyCurrentRunRecord(Cognex.VisionPro.ICogRecord currentRecord)
  {
  }
  #endregion

  #region When the Last Run Record is Created
  /// <summary>
  /// Called when the last run record may have changed and is being reconstructed
  /// </summary>
  /// <param name="lastRecord">
  /// The new last run record is available to be initialized or customized.</param>
  public override void ModifyLastRunRecord(Cognex.VisionPro.ICogRecord lastRecord)
  {
    mToolBlock.AddGraphicToRunRecord(myLabel, lastRecord, "CogFixtureTool1.OutputImage", "");
    mToolBlock.AddGraphicToRunRecord(myLabel_1, lastRecord, "CogFixtureTool1.OutputImage", "");
    mToolBlock.AddGraphicToRunRecord(myLabel_2, lastRecord, "CogFixtureTool1.OutputImage", "");
  }
  #endregion

  #region When the Script is Initialized
  /// <summary>
  /// Perform any initialization required by your script here
  /// </summary>
  /// <param name="host">The host tool</param>
  public override void Initialize(Cognex.VisionPro.ToolGroup.CogToolGroup host)
  {
    // DO NOT REMOVE - Call the base class implementation first - DO NOT REMOVE
    base.Initialize(host);

    // Store a local copy of the script host
    this.mToolBlock = ((Cognex.VisionPro.ToolBlock.CogToolBlock)(host));
  }
  #endregion
}

```
