<div align="center">

## Dynamic Font Generation


</div>

### Description

Create text graphics on the fly using GD and freetype libraries. Supports any TrueType font and allows size, color, and bgcolor specification. Also has built in support for date formatting. Examples can be found at http://www.danhendricks.com/site.content/howto/php/dynafont/index.html
 
### More Info
 
The only required inputs are the 'text', but it is recommended you specify the font face and size as well.


<span>             |<span>
---                |---
**Submitted On**   |
**By**             |[Daniel M\. Hendricks](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByAuthor/daniel-m-hendricks.md)
**Level**          |Intermediate
**User Rating**    |5.0 (10 globes from 2 users)
**Compatibility**  |PHP 4\.0
**Category**       |[Graphics/ Sound](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByCategory/graphics-sound__8-15.md)
**World**          |[PHP](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByWorld/php.md)
**Archive File**   |[](https://github.com/Planet-Source-Code/daniel-m-hendricks-dynamic-font-generation__8-1162/archive/master.zip)

### API Declarations

The IMAGE SUPPORT FUNCTIONS lifted from code by another author, but I made the other routines to make it useful and easier to use.


### Source Code

```
<?
// SETTINGS
$fontbase = $DOCUMENT_ROOT."/howto/php/dynafont/";
$rotateDegrees = 0;
$text = trim(stripslashes($_GET["text"]));
// SET FONT PROPERTIES
$font = $_GET["font"];
if (!$font) { $font = "arial.ttf"; }
$fontfile = $fontbase.$font;
$size = $_GET["size"];
if (!$size) { $size = 5; }
$textalign = $_GET["textalign"];
if (!$textalign) { $textalign = "left"; }
$color = $_GET["color"];
if (!$color) { $color = "000000"; }
//$foreground = GetRGBvalues("0x".$color, false);
$bgcolor = $_GET["bgcolor"];
if (!$bgcolor) { $bgcolor = "FFFFFF"; }
//$background = GetRGBvalues("0x".$bgcolor, false);
if($_GET["date"]) {
 $text = date($_GET["date"]);
}
$ImageSizeInfoArr = GetImageSizeForTextBlock($rotateDegrees, $text, $fontfile, $size);
// CREATE IMAGE
$im = imagecreate ($ImageSizeInfoArr["WIDTH"]+5, $ImageSizeInfoArr["HEIGHT"]);
$background = ImageColorAllocate ($im, hexdec(substr($bgcolor, 0, 2)), hexdec(substr($bgcolor, 2, 2)), hexdec(substr($bgcolor, 4, 2)));
$foreground = ImageColorAllocate ($im, hexdec(substr($color, 0, 2)), hexdec(substr($color, 2, 2)), hexdec(substr($color, 4, 2)));
ImageTTFText ($im, $size, 0, $ImageSizeInfoArr["X_POS"], $ImageSizeInfoArr["Y_POS"], $foreground, $fontfile, $text);
// SEND IMAGE TO BROWSER
Header ("Content-type: image/PNG");
ImagePNG ($im, "", 100);
ImageDestroy ($im);
?>
<?
// IMAGE SUPPORT FUNCTIONS
function GetImageSizeForTextBlock($rotateDegrees, $text, $fontLocation, $fontSize){
 ##-- This will add a small margin area around the text block.  --##
 ##-- The margin grows with the font size.   --##
 $margin = intval(0.25 * $fontSize);
 ##-- Convert the angle so it is always a number between 0 and 359 --##
 if($rotateDegrees > 359 || $rotateDegrees < -359){
  $rotateDegrees = intval($rotateDegrees % 360);
 }
 if($rotateDegrees < 0){
  $rotateDegrees = 360 + $rotateDegrees;
 }
 #-- Clean out all of the "new line" characters. --#
 #-- The following function expects the special code of "-br-" for line breaks. --#
 $text = preg_replace("/(\n|\r)/", "", $text);
 ##-- Get the total size of the text block so that we know how big to create the Image --##
 $TextBoxSize = ImageTTFBBox ($fontSize, $rotateDegrees, $fontLocation, preg_replace("/-br-/", "\n\r", $text));
 ##-- Put the variables into reader-friendly variables --##
 $TxtBx_Lwr_L_x = $TextBoxSize[0];
 $TxtBx_Lwr_L_y = $TextBoxSize[1];
 $TxtBx_Lwr_R_x = $TextBoxSize[2];
 $TxtBx_Lwr_R_y = $TextBoxSize[3];
 $TxtBx_Upr_R_x = $TextBoxSize[4];
 $TxtBx_Upr_R_y = $TextBoxSize[5];
 $TxtBx_Upr_L_x = $TextBoxSize[6];
 $TxtBx_Upr_L_y = $TextBoxSize[7];
 ##-- The Text Box coordinates are relative to the font, regardless of the angle   --##
 ##-- We need to figure out the height and width of the text box accounting for the rotation  --##
 if($rotateDegrees <= 90 || $rotateDegrees >= 270 ){
  $TextBox_width = max($TxtBx_Lwr_R_x, $TxtBx_Upr_R_x) - min($TxtBx_Lwr_L_x, $TxtBx_Upr_L_x);
  $TextBox_height = max($TxtBx_Lwr_L_y, $TxtBx_Lwr_R_y) - min($TxtBx_Upr_R_y, $TxtBx_Upr_L_y);
  ##-- This figures out where the coordinates of the first letter in the text block starts --##
  ##-- It is roughly the lower left-hand corner letter      --##
  $Start_Text_Coord_x = -(min($TxtBx_Upr_L_x, $TxtBx_Lwr_L_x));
  $Start_Text_Coord_y = -(min($TxtBx_Upr_R_y, $TxtBx_Upr_L_y));
 }
 else{
  $TextBox_width = max($TxtBx_Lwr_L_x, $TxtBx_Upr_L_x) - min($TxtBx_Lwr_R_x, $TxtBx_Upr_R_x);
  $TextBox_height = max($TxtBx_Upr_R_y, $TxtBx_Upr_L_y) - min($TxtBx_Lwr_L_y, $TxtBx_Lwr_R_y);
  $Start_Text_Coord_x = -(min($TxtBx_Lwr_R_x,$TxtBx_Upr_R_x));
  $Start_Text_Coord_y = -(min($TxtBx_Lwr_L_y, $TxtBx_Lwr_R_y));
 }
 ##-- We need to add our margin to the coordinates of the first letter --##
 $Start_Text_Coord_x += $margin;
 $Start_Text_Coord_y += $margin - 2; //Don't forget to account for the '0th' pixel at Y-coord 0
 ##-- We are going to make the image just big enough to hold our text block... accounting for the rotation and font size. --##
 ##-- We times the Margin by 2 so that there is a margin on all 4 sides  --##
 $TotalImageWidth = $TextBox_width + $margin *2;
 $TotalImageHeight = $TextBox_height + $margin *2;
 ##-- Send back a hash with our calculations --#
 return array("WIDTH"=>$TotalImageWidth, "HEIGHT"=>$TotalImageHeight, "X_POS"=>$Start_Text_Coord_x, "Y_POS"=>$Start_Text_Coord_y);
}
?>
```

