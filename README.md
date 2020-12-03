# libKitten
Library to get three different colors from an image and detect if one is light or dark

## How to use

### Installation:
Download this repository, put the .dylib into your theos/lib folder and the Kitten folder into your theos/include folder

### Importing:
Import the library in your header or main file `#import <Kitten/libKitten.h>`<br>
Add the library to your Makefile `$(TWEAK_NAME)_LIBRARIES = kitten`<br>
Make libKitten a dependency of your tweak in your control file `Depends: love.litten.libkitten`<br>
Make sure the user also adds my repo (https://repo.litten.love) to be able to download it

### Usage:
You can create variables to use later on like this:<br>
`UIColor* backgroundColor = [libKitten backgroundColor:theImageYouWantToCalculate];`<br>
`UIColor* primaryColor = [libKitten primaryColor:theImageYouWantToCalculate];`<br>
`UIColor* secondaryColor = [libKitten secondaryColor:theImageYouWantToCalculate];`<br>
The parameter "theImageYouWantToCalculate" is the UIImage you want to get the color from; Now you can just use it wherever you need it
<br><br>
You don't have to create variables of course, `[label setTextColor:[libKitten primaryColor:theImageYouWantToCalculate]];` works as well

## Example
In this example i want to set the color of the lockscreen time based on the lockscreen wallpaper

```
#import  <UIKit/UIKit.h>
#import  <Kitten/libKitten.h>

extern CFArrayRef CPBitmapCreateImagesFromData(CFDataRef cpbitmap, void*, int, void*);

@interface  SBFLockScreenDateView : UIView
@property(nonatomic, retain)UIColor* textColor;
@end

%group ExampleTweak

%hook SBFLockScreenDateView

- (void)didMoveToWindow {

 	%orig;

 	// get lockscreen wallpaper
 	NSData* lockWallpaperData = [NSData dataWithContentsOfFile:@"/var/mobile/Library/SpringBoard/LockBackground.cpbitmap"];
	CFDataRef lockWallpaperDataRef = (__bridge CFDataRef)lockWallpaperData;
	NSArray* imageArray = (__bridge NSArray *)CPBitmapCreateImagesFromData(lockWallpaperDataRef, NULL, 1, NULL);
	UIImage* wallpaper = [UIImage imageWithCGImage:(CGImageRef)imageArray[0]];

 	// get the color and set it
 	[self setTextColor:[libKitten secondaryColor:wallpaper]];

}

%end

%hook SBUILegibilityLabel // needed to change SBUILegibilityLabel colors

- (BOOL)_needsColorImage {

 	return  YES;  

}

%end

%end

%ctor {

 	%init(ExampleTweak);

}
```
