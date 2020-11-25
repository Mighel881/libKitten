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

### Setting it up:
Now you can create a new instance of it and give your kitten a cute name `libKitten* nena;` (my cat's name is nena) <br>
Next step is to initialize it (for example in your constructor) `instanceName = [[libKitten alloc] init];`

### Usage:
You can create variables to use later on like this:<br>
`UIColor* backgroundColor = [instanceName backgroundColor:theImageYouWantToCalculate];`<br>
`UIColor* primaryColor = [instanceName primaryColor:theImageYouWantToCalculate];`<br>
`UIColor* secondaryColor = [instanceName secondaryColor:theImageYouWantToCalculate];`<br>
The parameter "theImageYouWantToCalculate" is the image you want to get the color from; Now you can just use it wherever you need it
<br><br>
You don't have to create variables of course, `[label setTextColor:[instanceName primaryColor:theImageYouWantToCalculate]];` works as well

## Example
In this example i want to set the color of the lockscreen time based on the lockscreen wallpaper

```
#import  <UIKit/UIKit.h>
#import  <Kitten/libKitten.h>

libKitten* nena;

extern CFArrayRef CPBitmapCreateImagesFromData(CFDataRef cpbitmap, void*, int, void*);

@interface  SBFLockScreenDateView : UIView
@property(nonatomic, retain)UIColor* textColor;
@end

%group ExampleTweak

%hook SBFLockScreenDateView

- (void)didMoveToWindow {

 	%orig;

 	// get homescreen wallpaper
 	NSData* homeWallpaperData = [NSData dataWithContentsOfFile:@"/var/mobile/Library/SpringBoard/LockBackground.cpbitmap"];
	CFDataRef homeWallpaperDataRef = (__bridge CFDataRef)homeWallpaperData;
	NSArray* imageArray = (__bridge NSArray *)CPBitmapCreateImagesFromData(homeWallpaperDataRef, NULL, 1, NULL);
	wallpaper = [UIImage imageWithCGImage:(CGImageRef)imageArray[0]];

 	// get the color and set it
 	[self setTextColor:[nena secondaryColor:wallpaper]];

}

%end

%hook SBUILegibilityLabel // needed to change SBUILegibilityLabel colors

- (BOOL)_needsColorImage {

 	return  YES;  

}

%end

%end

%ctor {

 	nena = [[libKitten alloc] init];
 	%init(ExampleTweak);

}
```
