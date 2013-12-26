---
layout: post
title: "使用摄像头影像作为背景"
date: 2013-12-26 23:36:47 +0800
comments: true
categories: iOS Cocos2d
---

参考[https://github.com/kyleroche/Professional_iOS_AugmentedReality]()
###只需要影像和拍照图片
```
@try {
    imagePicker = [[[UIImagePickerController alloc] init] autorelease];
    imagePicker.sourceType = UIImagePickerControllerSourceTypeCamera;
    imagePicker.showsCameraControls = NO;
    imagePicker.toolbarHidden = YES;
    imagePicker.navigationBarHidden = YES;
    imagePicker.wantsFullScreenLayout = YES;
    imagePicker.cameraViewTransform = CGAffineTransformScale(imagePicker.cameraViewTransform, 1.0, 1.3);
}
@catch (NSException * e) {
    [imagePicker release];
     imagePicker = nil;
}
@finally {
    if(imagePicker) {
        [cameraView addSubview:[imagePicker view]];//直接把view加上去
        [cameraView release];
    }
}
```
或者使用AVCaptureVideoPreviewLayer显示影像和AVCaptureStillImageOutput获取图片
```
//显示影像
AVCaptureSession *session = [[AVCaptureSession alloc] init];
session.sessionPreset = AVCaptureSessionPresetMedium;
    
AVCaptureVideoPreviewLayer *captureVideoPreviewLayer = [[AVCaptureVideoPreviewLayer alloc] initWithSession:session];
captureVideoPreviewLayer.frame = self.videoPreview.bounds;
[self.videoPreview.layer addSublayer:captureVideoPreviewLayer];
    
AVCaptureDevice *device = [AVCaptureDevice defaultDeviceWithMediaType:AVMediaTypeVideo];
    
NSError *error = nil;
AVCaptureDeviceInput *input = [AVCaptureDeviceInput deviceInputWithDevice:device error:&error];
if (!input) {
    NSLog(@"ERROR: trying to open camera: %@", error);
}
[session addInput:input];
    
stillImageOutput = [[AVCaptureStillImageOutput alloc] init];
NSDictionary *outputSettings = [[NSDictionary alloc] initWithObjectsAndKeys: AVVideoCodecJPEG, AVVideoCodecKey, nil];
[stillImageOutput setOutputSettings:outputSettings];
    
[session addOutput:stillImageOutput];
    
[session startRunning];

//获取图片
AVCaptureConnection *videoConnection = nil;
for (AVCaptureConnection *connection in stillImageOutput.connections)
{
    for (AVCaptureInputPort *port in [connection inputPorts])
    {
        if ([[port mediaType] isEqual:AVMediaTypeVideo] )
        {
            videoConnection = connection;
            break;
        }
    }
    if (videoConnection) { break; }
}

[stillImageOutput captureStillImageAsynchronouslyFromConnection:videoConnection completionHandler: ^(CMSampleBufferRef imageSampleBuffer, NSError *error)
{
    CFDictionaryRef exifAttachments = CMGetAttachment( imageSampleBuffer, kCGImagePropertyExifDictionary, NULL);
    if (exifAttachments)
    {
        // Do something with the attachments.
        NSLog(@"attachements: %@", exifAttachments);
    }
    else
       NSLog(@"no attachments");
     
    NSData *imageData = [AVCaptureStillImageOutput jpegStillImageNSDataRepresentation:imageSampleBuffer];
    UIImage *image = [[UIImage alloc] initWithData:imageData];
     
    self.videoImage.image = image;
}];
```
###需要显示图片和处理每一帧
```
//显示影像，把上面的stillImage部分换成
AVCaptureVideoDataOutput *output = [[AVCaptureVideoDataOutput alloc] init];
[_session addOutput:output];
    
dispatch_queue_t queue = dispatch_queue_create("pumpkins", NULL);
[output setSampleBufferDelegate:self queue:queue];
dispatch_release(queue);
    
output.videoSettings = 
[NSDictionary dictionaryWithObject:[NSNumber numberWithInt:kCVPixelFormatType_32BGRA] forKey:(id)kCVPixelBufferPixelFormatTypeKey];
    
[_session startRunning];
//获取图片在代理中
- (UIImage *) imageFromSampleBuffer:(CMSampleBufferRef) sampleBuffer 
{
}
```
相关方法参考[https://github.com/kyleroche/Professional_iOS_AugmentedReality/blob/master/Ch8/Ch8/AppDelegate.m]()
###同时他的例子中,通过下面来使Cocos2d和UiViewController共用同一个View
```
EAGLView *glView = [EAGLView viewWithFrame:[window bounds] pixelFormat: kEAGLColorFormatRGBA8 depthFormat:0];
[director setOpenGLView:glView];
[viewController setView:glView];
```
