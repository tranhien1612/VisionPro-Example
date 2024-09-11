# Phát hiện lỗi trên vật thể

## Image Source
Click ```Choose Folder```, chọn thư mục chứa ảnh

## CogPMAlignTool
Nhấn ```Grab Train Image```, sau đó chọn ```Current.TrainImage``` ở list (góc trên bên phải), sau đó kéo để khoanh vùng vật thể cần detect.

Sang tab ```Train Region & Origin``` chọn ```Center Origin``` để đặt gốc trụ tọa độ vào tâm của vật thể. Sau đó nhấn ```Train```.

Tab ```Graphic```, tích chọn ```Show Fine```.

## CogHistogramTool
Phân tích biểu đồ histogram của vùng bất kỳ trên vật thể

## C# Advance Script để hiển thị text lên vật thể

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
using Cognex.VisionPro.ImageProcessing;
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

    CogPMAlignTool obj = mToolBlock.Tools["CogPMAlignTool1"] as CogPMAlignTool;
    CogHistogramTool his1 = mToolBlock.Tools["CogHistogramTool1"] as CogHistogramTool;
    CogHistogramTool his2 = mToolBlock.Tools["CogHistogramTool2"] as CogHistogramTool;
    
    myLabel.Font = new Font("Cambrial", 20);
    if(his1.Result.Mean > 50 || his2.Result.Mean > 50){
      myLabel.SetXYText(obj.Results[0].GetPose().TranslationX, obj.Results[0].GetPose().TranslationY, "NG");
      myLabel.Color = CogColorConstants.Red;
    }else{
      myLabel.SetXYText(obj.Results[0].GetPose().TranslationX, obj.Results[0].GetPose().TranslationY, "OK");
      myLabel.Color = CogColorConstants.Green;
    }
    
    
    
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
    mToolBlock.AddGraphicToRunRecord(myLabel, lastRecord, "CogPMAlignTool1.InputImage", "");
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
