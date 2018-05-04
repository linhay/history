---
title: iOS-PingFangSC(苹方-简)字体使用
date: 2018-05-02
categories: "iOS"
tags: 
     - siwft
     - ios
---

### 获取当前系统支持字体册

```swift
    UIFont.familyNames.forEach { (familyName) in
      print(familyName)
      UIFont.fontNames(forFamilyName: familyName).forEach { (item) in
        print(" - " + item)
      }
    }
```

### 使用指定字体

```swift
let font = UIFont(name: "PingFangSC-Regular", size: 18) 
```

<!--more-->

## 加载第三方字体

### 静态加载

1. **添加字体文件**

2. **info.plist修改**

   - 增加`Fonts provided by application`字段
   - 在该字段下添加引入的字体文件名

   ![](https://s.linhey.com/iOS-PingFangSC%E5%AD%97%E4%BD%93%E4%BD%BF%E7%94%A8.png)

###  动态加载

通过指定字体文件路径加载,该种方式可配合 **网络下发/字体加密** 使用.

```swift
 /// 加载本地字体文件
func custom(fileURL: URL, size: CGFloat) -> UIFont? {
	guard let dataProvider = CGDataProvider(url: fileURL as CFURL),
    let cgFont = CGFont(dataProvider),
      /// 注册字体
	CTFontManagerRegisterGraphicsFont(cgFont, nil),
	let fontName = cgFont.postScriptName as String? else { return nil }
    return UIFont(name: fontName, size: size)
  }
```



### 动态下载苹果提供的多种中文字体

从 iOS 6 开始，苹果提供了一个新的 API 可以让我们动态下载苹果提供的额外字体。并且，这些字体是下载到系统中，也就是说，如果其他 App 也使用了这个字体或者 App 删除后重装时是无需再额外下载这个字体的。

[OC 官方Demo](https://developer.apple.com/library/content/samplecode/DownloadFont/Listings/DownloadFont_ViewController_m.html) / [swift Demo](https://github.com/linhay/Apple-Demos/tree/master/DownloadFont)

1. 判断字体是否存在

   ```swift
       if let aFont = UIFont(name: fontName, size: 12),
         aFont.fontName == fontName || aFont.familyName == fontName{
   	// 存在
       }
   ```

2. 下载(具体代码请参照[swift Demo](https://github.com/linhay/Apple-Demos/tree/master/DownloadFont))

   ```swift
   let attrs = [kCTFontNameAttribute:fontName]
   let desc = CTFontDescriptorCreateWithAttributes(attrs as CFDictionary)
   let descs = [desc]
   CTFontDescriptorMatchFontDescriptorsWithProgressHandler(descs as CFArray, nil) { (state, progressParameter) -> Bool in
   // 下载
   }     
   ```

   

##### 注意点

1. iOS 9 以下 系统 默认没有`PingFang`系列字体.

2. 使用`UIFont`时`name`使用字体`postScriptName`.~~(有些字体文件与真实名称不符)~~,可以使用字体册查看具体名称.

   ![](https://s.linhey.com/iOS-PingFangSC%E5%AD%97%E4%BD%93%E4%BD%BF%E7%94%A8-2.png)



#### 附录:

1. iOS 11系统提供字体

```
Copperplate
 - Copperplate-Light
 - Copperplate
 - Copperplate-Bold
Heiti SC
Apple SD Gothic Neo
 - AppleSDGothicNeo-Thin
 - AppleSDGothicNeo-Light
 - AppleSDGothicNeo-Regular
 - AppleSDGothicNeo-Bold
 - AppleSDGothicNeo-SemiBold
 - AppleSDGothicNeo-UltraLight
 - AppleSDGothicNeo-Medium
Thonburi
 - Thonburi
 - Thonburi-Light
 - Thonburi-Bold
Gill Sans
 - GillSans-Italic
 - GillSans-SemiBold
 - GillSans-UltraBold
 - GillSans-Light
 - GillSans-Bold
 - GillSans
 - GillSans-SemiBoldItalic
 - GillSans-BoldItalic
 - GillSans-LightItalic
Marker Felt
 - MarkerFelt-Thin
 - MarkerFelt-Wide
Hiragino Maru Gothic ProN
 - HiraMaruProN-W4
Courier New
 - CourierNewPS-ItalicMT
 - CourierNewPSMT
 - CourierNewPS-BoldItalicMT
 - CourierNewPS-BoldMT
Kohinoor Telugu
 - KohinoorTelugu-Regular
 - KohinoorTelugu-Medium
 - KohinoorTelugu-Light
Heiti TC
Avenir Next Condensed
 - AvenirNextCondensed-Heavy
 - AvenirNextCondensed-MediumItalic
 - AvenirNextCondensed-Regular
 - AvenirNextCondensed-UltraLightItalic
 - AvenirNextCondensed-Medium
 - AvenirNextCondensed-HeavyItalic
 - AvenirNextCondensed-DemiBoldItalic
 - AvenirNextCondensed-Bold
 - AvenirNextCondensed-DemiBold
 - AvenirNextCondensed-BoldItalic
 - AvenirNextCondensed-Italic
 - AvenirNextCondensed-UltraLight
Tamil Sangam MN
 - TamilSangamMN
 - TamilSangamMN-Bold
Helvetica Neue
 - HelveticaNeue-UltraLightItalic
 - HelveticaNeue-Medium
 - HelveticaNeue-MediumItalic
 - HelveticaNeue-UltraLight
 - HelveticaNeue-Italic
 - HelveticaNeue-Light
 - HelveticaNeue-ThinItalic
 - HelveticaNeue-LightItalic
 - HelveticaNeue-Bold
 - HelveticaNeue-Thin
 - HelveticaNeue-CondensedBlack
 - HelveticaNeue
 - HelveticaNeue-CondensedBold
 - HelveticaNeue-BoldItalic
Gurmukhi MN
 - GurmukhiMN-Bold
 - GurmukhiMN
Georgia
 - Georgia-BoldItalic
 - Georgia-Italic
 - Georgia
 - Georgia-Bold
Times New Roman
 - TimesNewRomanPS-ItalicMT
 - TimesNewRomanPS-BoldItalicMT
 - TimesNewRomanPS-BoldMT
 - TimesNewRomanPSMT
Sinhala Sangam MN
 - SinhalaSangamMN-Bold
 - SinhalaSangamMN
Arial Rounded MT Bold
 - ArialRoundedMTBold
Kailasa
 - Kailasa-Bold
 - Kailasa
Kohinoor Devanagari
 - KohinoorDevanagari-Regular
 - KohinoorDevanagari-Light
 - KohinoorDevanagari-Semibold
Kohinoor Bangla
 - KohinoorBangla-Regular
 - KohinoorBangla-Semibold
 - KohinoorBangla-Light
Chalkboard SE
 - ChalkboardSE-Bold
 - ChalkboardSE-Light
 - ChalkboardSE-Regular
Apple Color Emoji
 - AppleColorEmoji
PingFang TC
 - PingFangTC-Regular
 - PingFangTC-Thin
 - PingFangTC-Medium
 - PingFangTC-Semibold
 - PingFangTC-Light
 - PingFangTC-Ultralight
Gujarati Sangam MN
 - GujaratiSangamMN
 - GujaratiSangamMN-Bold
Geeza Pro
 - GeezaPro-Bold
 - GeezaPro
Damascus
 - DamascusBold
 - DamascusLight
 - Damascus
 - DamascusMedium
 - DamascusSemiBold
Noteworthy
 - Noteworthy-Bold
 - Noteworthy-Light
Avenir
 - Avenir-Oblique
 - Avenir-HeavyOblique
 - Avenir-Heavy
 - Avenir-BlackOblique
 - Avenir-BookOblique
 - Avenir-Roman
 - Avenir-Medium
 - Avenir-Black
 - Avenir-Light
 - Avenir-MediumOblique
 - Avenir-Book
 - Avenir-LightOblique
Mishafi
 - DiwanMishafi
Academy Engraved LET
 - AcademyEngravedLetPlain
Futura
 - Futura-CondensedExtraBold
 - Futura-Medium
 - Futura-Bold
 - Futura-CondensedMedium
 - Futura-MediumItalic
Party LET
 - PartyLetPlain
Kannada Sangam MN
 - KannadaSangamMN-Bold
 - KannadaSangamMN
Arial Hebrew
 - ArialHebrew-Bold
 - ArialHebrew-Light
 - ArialHebrew
Farah
 - Farah
Arial
 - Arial-BoldMT
 - Arial-BoldItalicMT
 - Arial-ItalicMT
 - ArialMT
Chalkduster
 - Chalkduster
Kefa
 - Kefa-Regular
Hoefler Text
 - HoeflerText-Italic
 - HoeflerText-Black
 - HoeflerText-Regular
 - HoeflerText-BlackItalic
Optima
 - Optima-ExtraBlack
 - Optima-BoldItalic
 - Optima-Italic
 - Optima-Regular
 - Optima-Bold
Palatino
 - Palatino-Italic
 - Palatino-Roman
 - Palatino-BoldItalic
 - Palatino-Bold
Malayalam Sangam MN
 - MalayalamSangamMN-Bold
 - MalayalamSangamMN
Al Nile
 - AlNile
 - AlNile-Bold
Lao Sangam MN
 - LaoSangamMN
Bradley Hand
 - BradleyHandITCTT-Bold
Hiragino Mincho ProN
 - HiraMinProN-W3
 - HiraMinProN-W6
PingFang HK
 - PingFangHK-Medium
 - PingFangHK-Thin
 - PingFangHK-Regular
 - PingFangHK-Ultralight
 - PingFangHK-Semibold
 - PingFangHK-Light
Helvetica
 - Helvetica-Oblique
 - Helvetica-BoldOblique
 - Helvetica
 - Helvetica-Light
 - Helvetica-Bold
 - Helvetica-LightOblique
Courier
 - Courier-BoldOblique
 - Courier-Oblique
 - Courier
 - Courier-Bold
Cochin
 - Cochin-Italic
 - Cochin-Bold
 - Cochin
 - Cochin-BoldItalic
Trebuchet MS
 - TrebuchetMS-Bold
 - TrebuchetMS-Italic
 - Trebuchet-BoldItalic
 - TrebuchetMS
Devanagari Sangam MN
 - DevanagariSangamMN
 - DevanagariSangamMN-Bold
Oriya Sangam MN
 - OriyaSangamMN
 - OriyaSangamMN-Bold
Rockwell
 - Rockwell-Italic
 - Rockwell-Regular
 - Rockwell-Bold
 - Rockwell-BoldItalic
Snell Roundhand
 - SnellRoundhand
 - SnellRoundhand-Bold
 - SnellRoundhand-Black
Zapf Dingbats
 - ZapfDingbatsITC
Bodoni 72
 - BodoniSvtyTwoITCTT-Bold
 - BodoniSvtyTwoITCTT-BookIta
 - BodoniSvtyTwoITCTT-Book
Verdana
 - Verdana-Italic
 - Verdana
 - Verdana-Bold
 - Verdana-BoldItalic
American Typewriter
 - AmericanTypewriter-CondensedBold
 - AmericanTypewriter-Condensed
 - AmericanTypewriter-CondensedLight
 - AmericanTypewriter
 - AmericanTypewriter-Bold
 - AmericanTypewriter-Semibold
 - AmericanTypewriter-Light
Avenir Next
 - AvenirNext-Medium
 - AvenirNext-DemiBoldItalic
 - AvenirNext-DemiBold
 - AvenirNext-HeavyItalic
 - AvenirNext-Regular
 - AvenirNext-Italic
 - AvenirNext-MediumItalic
 - AvenirNext-UltraLightItalic
 - AvenirNext-BoldItalic
 - AvenirNext-Heavy
 - AvenirNext-Bold
 - AvenirNext-UltraLight
Baskerville
 - Baskerville-SemiBoldItalic
 - Baskerville-SemiBold
 - Baskerville-BoldItalic
 - Baskerville
 - Baskerville-Bold
 - Baskerville-Italic
Khmer Sangam MN
 - KhmerSangamMN
Didot
 - Didot-Bold
 - Didot
 - Didot-Italic
Savoye LET
 - SavoyeLetPlain
Bodoni Ornaments
 - BodoniOrnamentsITCTT
Symbol
 - Symbol
Charter
 - Charter-BlackItalic
 - Charter-Bold
 - Charter-Roman
 - Charter-Black
 - Charter-BoldItalic
 - Charter-Italic
Menlo
 - Menlo-BoldItalic
 - Menlo-Bold
 - Menlo-Italic
 - Menlo-Regular
Noto Nastaliq Urdu
 - NotoNastaliqUrdu
Bodoni 72 Smallcaps
 - BodoniSvtyTwoSCITCTT-Book
DIN Alternate
 - DINAlternate-Bold
Papyrus
 - Papyrus-Condensed
 - Papyrus
Hiragino Sans
 - HiraginoSans-W3
 - HiraginoSans-W6
PingFang SC
 - PingFangSC-Medium
 - PingFangSC-Semibold
 - PingFangSC-Light
 - PingFangSC-Ultralight
 - PingFangSC-Regular
 - PingFangSC-Thin
Myanmar Sangam MN
 - MyanmarSangamMN
 - MyanmarSangamMN-Bold
Zapfino
 - Zapfino
Telugu Sangam MN
Bodoni 72 Oldstyle
 - BodoniSvtyTwoOSITCTT-BookIt
 - BodoniSvtyTwoOSITCTT-Book
 - BodoniSvtyTwoOSITCTT-Bold
Euphemia UCAS
 - EuphemiaUCAS
 - EuphemiaUCAS-Italic
 - EuphemiaUCAS-Bold
Bangla Sangam MN
DIN Condensed
 - DINCondensed-Bold
```

