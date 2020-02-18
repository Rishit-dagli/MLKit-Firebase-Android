# MLKit-Firebase-Android 

![Build](https://img.shields.io/badge/build-passing-brightgreen)
![GitHub followers](https://img.shields.io/github/followers/Rishit-dagli?label=Follow&style=social)
[![Twitter](https://img.shields.io/twitter/url?style=social&url=https%3A%2F%2Fgithub.com%2FRishit-dagli%2Fpopup_box)](https://twitter.com/intent/tweet?text=Wow:&url=https://github.com/Rishit-dagli/MLKit-Firebase-Android)
![Twitter Follow](https://img.shields.io/twitter/follow/rishit_dagli?label=Follow&style=social)

## Table of contents

1. [Introduction](#Introduction)


2. [Key capabilities](#Key-capabilities)<br>
    2.1 [Production-ready for common use cases](#Production-ready-for-common-use-cases)<br>
    2.2 [On-device or in the cloud](#On-device-or-in-the-cloud )<br>
    2.3 [Deploy custom models](#Deploy-custom-models)<br>
   
   
3. [Example (Face detection)](#Example-(Face-detection))<br>
    3.1 [Before you begin](#Before-you-begin)<br>
    3.2 [Input image guidelines](#Input-image-guidelines)<br>
            &nbsp;&nbsp;&nbsp;&nbsp; 3.21 [Configure the face detector](#Configure-the-face-detector)<br>
            &nbsp;&nbsp;&nbsp;&nbsp; 3.22 [Run the face detector](#Run-the-face-detector)<br>
            &nbsp;&nbsp;&nbsp;&nbsp; 3.23 [Get information about detected faces](#Get-information-about-detected-faces)
        
        
4. [Examples](#Examples)


5. [What next!](#What-next!)


## Introduction
A face recognition, bar code scanning, image labeling, landmark recognition and text recognition either on cloud or edge with the Firebase SDKs 

ML Kit is a mobile SDK that brings Google's machine learning expertise to Android and iOS apps in a powerful yet easy-to-use package. Whether you're new or experienced in machine learning, you can implement the functionality you need in just a few lines of code. There's no need to have deep knowledge of neural networks or model optimization to get started. On the other hand, if you are an experienced ML developer, ML Kit provides convenient APIs that help you use your custom TensorFlow Lite models in your mobile apps.

Watch this video for a better understanding-

[![youtube.com/watch?v=ejrn_JHksws](http://img.youtube.com/vi/ejrn_JHksws/0.jpg)](http://www.youtube.com/watch?v=ejrn_JHksws "Introducing ML Kit")

## Key capabilities

1. Production-ready for common use cases 	

ML Kit comes with a set of ready-to-use APIs for common mobile use cases: recognizing text, detecting faces, identifying landmarks, scanning barcodes, labeling images, and identifying the language of text. Simply pass in data to the ML Kit library and it gives you the information you need.

2. On-device or in the cloud 	

ML Kit’s selection of APIs run on-device or in the cloud. Our on-device APIs can process your data quickly and work even when there’s no network connection. Our cloud-based APIs, on the other hand, leverage the power of Google Cloud Platform's machine learning technology to give you an even higher level of accuracy.

3. Deploy custom models 	

If ML Kit's APIs don't cover your use cases, you can always bring your own existing TensorFlow Lite models. Just upload your model to Firebase, and we'll take care of hosting and serving it to your app. ML Kit acts as an API layer to your custom model, making it simpler to run and use.

![](https://github.com/Rishit-dagli/MLKit-Firebase/blob/master/images/ML%20kit%20capabilities.png)

## Example (Face detection) 

With ML Kit's face detection API, you can detect faces in an image, identify key facial features, and get the contours of detected faces.

With face detection, you can get the information you need to perform tasks like embellishing selfies and portraits, or generating avatars from a user's photo. Because ML Kit can perform face detection in real time, you can use it in applications like video chat or games that respond to the player's expressions.

Note: we will only be seeing these example swith Android

![](https://github.com/Rishit-dagli/MLKit-Firebase-Android/blob/master/images/firebase%20face%20capability.JPG)

### Before you begin

If you haven't already, add Firebase to your Android project.

In your project-level build.gradle file, make sure to include Google's Maven repository in both your buildscript and allprojects sections.

Add the dependencies for the ML Kit Android libraries to your module (app-level) Gradle file (usually `app/build.gradle`):

    
    apply plugin: 'com.android.application'
    apply plugin: 'com.google.gms.google-services'

    dependencies {
      // ...

      implementation 'com.google.firebase:firebase-ml-vision:24.0.1'
      // If you want to detect face contours (landmark detection and classification
      // don't require this additional model):
      implementation 'com.google.firebase:firebase-ml-vision-face-model:19.0.0'
    }
    

Optional but recommended: Configure your app to automatically download the ML model to the device after your app is installed from the Play Store.

To do so, add the following declaration to your app's AndroidManifest.xml file:


    <application ...>
      ...
      <meta-data
          android:name="com.google.firebase.ml.vision.DEPENDENCIES"
          android:value="face" />
      <!-- To use multiple models: android:value="face,model2,model3" -->
    </application>


If you do not enable install-time model downloads, the model will be downloaded the first time you run the detector. Requests you make before the download has completed will produce no results.

### Input image guidelines

For ML Kit to accurately detect faces, input images must contain faces that are represented by sufficient pixel data. In general, each face you want to detect in an image should be at least 100x100 pixels. If you want to detect the contours of faces, ML Kit requires higher resolution input: each face should be at least 200x200 pixels.

If you are detecting faces in a real-time application, you might also want to consider the overall dimensions of the input images. Smaller images can be processed faster, so to reduce latency, capture images at lower resolutions (keeping in mind the above accuracy requirements) and ensure that the subject's face occupies as much of the image as possible. Also see Tips to improve real-time performance.

Poor image focus can hurt accuracy. If you aren't getting acceptable results, try asking the user to recapture the image.

The orientation of a face relative to the camera can also affect what facial features ML Kit detects. See Face Detection Concepts.

1. Configure the face detector

Before you apply face detection to an image, if you want to change any of the face detector's default settings, specify those settings with a FirebaseVisionFaceDetectorOptions object. You can change the following settings:

![](https://github.com/Rishit-dagli/MLKit-Firebase/blob/master/images/firebase%20face%20settings.JPG)

For example:

* Java:

        // High-accuracy landmark detection and face classification
        FirebaseVisionFaceDetectorOptions highAccuracyOpts =
                new FirebaseVisionFaceDetectorOptions.Builder()
                        .setPerformanceMode(FirebaseVisionFaceDetectorOptions.ACCURATE)
                        .setLandmarkMode(FirebaseVisionFaceDetectorOptions.ALL_LANDMARKS)
                        .setClassificationMode(FirebaseVisionFaceDetectorOptions.ALL_CLASSIFICATIONS)
                        .build();

        // Real-time contour detection of multiple faces
        FirebaseVisionFaceDetectorOptions realTimeOpts =
                new FirebaseVisionFaceDetectorOptions.Builder()
                        .setContourMode(FirebaseVisionFaceDetectorOptions.ALL_CONTOURS)
                        .build();

* Kotlin         

        // High-accuracy landmark detection and face classification
        val highAccuracyOpts = FirebaseVisionFaceDetectorOptions.Builder()
                .setPerformanceMode(FirebaseVisionFaceDetectorOptions.ACCURATE)
                .setLandmarkMode(FirebaseVisionFaceDetectorOptions.ALL_LANDMARKS)
                .setClassificationMode(FirebaseVisionFaceDetectorOptions.ALL_CLASSIFICATIONS)
                .build()

        // Real-time contour detection of multiple faces
        val realTimeOpts = FirebaseVisionFaceDetectorOptions.Builder()
                .setContourMode(FirebaseVisionFaceDetectorOptions.ALL_CONTOURS)
                .build()
            
2. Run the face detector

To detect faces in an image, create a FirebaseVisionImage object from either a Bitmap, media.Image, ByteBuffer, byte array, or a file on the device. Then, pass the FirebaseVisionImage object to the FirebaseVisionFaceDetector's detectInImage method.

For face recognition, you should use an image with dimensions of at least 480x360 pixels. If you are recognizing faces in real time, capturing frames at this minimum resolution can help reduce latency.

Create a FirebaseVisionImage object from your image.

To create a FirebaseVisionImage object from a media.Image object, such as when capturing an image from a device's camera, pass the media.Image object and the image's rotation to FirebaseVisionImage.fromMediaImage().

If you use the CameraX library, the OnImageCapturedListener and ImageAnalysis.Analyzer classes calculate the rotation value for you, so you just need to convert the rotation to one of ML Kit's ROTATION_ constants before calling `FirebaseVisionImage.fromMediaImage():`

* Java

        private class YourAnalyzer implements ImageAnalysis.Analyzer {

            private int degreesToFirebaseRotation(int degrees) {
                switch (degrees) {
                    case 0:
                        return FirebaseVisionImageMetadata.ROTATION_0;
                    case 90:
                        return FirebaseVisionImageMetadata.ROTATION_90;
                    case 180:
                        return FirebaseVisionImageMetadata.ROTATION_180;
                    case 270:
                        return FirebaseVisionImageMetadata.ROTATION_270;
                    default:
                        throw new IllegalArgumentException(
                                "Rotation must be 0, 90, 180, or 270.");
                }
            }

            @Override
            public void analyze(ImageProxy imageProxy, int degrees) {
                if (imageProxy == null || imageProxy.getImage() == null) {
                    return;
                }
                Image mediaImage = imageProxy.getImage();
                int rotation = degreesToFirebaseRotation(degrees);
                FirebaseVisionImage image =
                        FirebaseVisionImage.fromMediaImage(mediaImage, rotation);
                // Pass image to an ML Kit Vision API
                // ...
            }
        }

* Kotlin

```
private class YourImageAnalyzer : ImageAnalysis.Analyzer {
    private fun degreesToFirebaseRotation(degrees: Int): Int = when(degrees) {
        0 -> FirebaseVisionImageMetadata.ROTATION_0
        90 -> FirebaseVisionImageMetadata.ROTATION_90
        180 -> FirebaseVisionImageMetadata.ROTATION_180
        270 -> FirebaseVisionImageMetadata.ROTATION_270
        else -> throw Exception("Rotation must be 0, 90, 180, or 270.")
    }

    override fun analyze(imageProxy: ImageProxy?, degrees: Int) {
        val mediaImage = imageProxy?.image
        val imageRotation = degreesToFirebaseRotation(degrees)
        if (mediaImage != null) {
            val image = FirebaseVisionImage.fromMediaImage(mediaImage, imageRotation)
            // Pass image to an ML Kit Vision API
            // ...
        }
    }
}

```

If you don't use a camera library that gives you the image's rotation, you can calculate it from the device's rotation and the orientation of camera sensor in the device:

* Java

```
    private static final SparseIntArray ORIENTATIONS = new SparseIntArray();
    static {
        ORIENTATIONS.append(Surface.ROTATION_0, 90);
        ORIENTATIONS.append(Surface.ROTATION_90, 0);
        ORIENTATIONS.append(Surface.ROTATION_180, 270);
        ORIENTATIONS.append(Surface.ROTATION_270, 180);
    }

    /**
     * Get the angle by which an image must be rotated given the device's current
     * orientation.
     */
    @RequiresApi(api = Build.VERSION_CODES.LOLLIPOP)
    private int getRotationCompensation(String cameraId, Activity activity, Context context)
            throws CameraAccessException {
        // Get the device's current rotation relative to its "native" orientation.
        // Then, from the ORIENTATIONS table, look up the angle the image must be
        // rotated to compensate for the device's rotation.
        int deviceRotation = activity.getWindowManager().getDefaultDisplay().getRotation();
        int rotationCompensation = ORIENTATIONS.get(deviceRotation);

        // On most devices, the sensor orientation is 90 degrees, but for some
        // devices it is 270 degrees. For devices with a sensor orientation of
        // 270, rotate the image an additional 180 ((270 + 270) % 360) degrees.
        CameraManager cameraManager = (CameraManager) context.getSystemService(CAMERA_SERVICE);
        int sensorOrientation = cameraManager
                .getCameraCharacteristics(cameraId)
                .get(CameraCharacteristics.SENSOR_ORIENTATION);
        rotationCompensation = (rotationCompensation + sensorOrientation + 270) % 360;

        // Return the corresponding FirebaseVisionImageMetadata rotation value.
        int result;
        switch (rotationCompensation) {
            case 0:
                result = FirebaseVisionImageMetadata.ROTATION_0;
                break;
            case 90:
                result = FirebaseVisionImageMetadata.ROTATION_90;
                break;
            case 180:
                result = FirebaseVisionImageMetadata.ROTATION_180;
                break;
            case 270:
                result = FirebaseVisionImageMetadata.ROTATION_270;
                break;
            default:
                result = FirebaseVisionImageMetadata.ROTATION_0;
                Log.e(TAG, "Bad rotation value: " + rotationCompensation);
        }
        return result;
    }
```

* Kotlin

```
private val ORIENTATIONS = SparseIntArray()

init {
    ORIENTATIONS.append(Surface.ROTATION_0, 90)
    ORIENTATIONS.append(Surface.ROTATION_90, 0)
    ORIENTATIONS.append(Surface.ROTATION_180, 270)
    ORIENTATIONS.append(Surface.ROTATION_270, 180)
}
/**
 * Get the angle by which an image must be rotated given the device's current
 * orientation.
 */
@RequiresApi(api = Build.VERSION_CODES.LOLLIPOP)
@Throws(CameraAccessException::class)
private fun getRotationCompensation(cameraId: String, activity: Activity, context: Context): Int {
    // Get the device's current rotation relative to its "native" orientation.
    // Then, from the ORIENTATIONS table, look up the angle the image must be
    // rotated to compensate for the device's rotation.
    val deviceRotation = activity.windowManager.defaultDisplay.rotation
    var rotationCompensation = ORIENTATIONS.get(deviceRotation)

    // On most devices, the sensor orientation is 90 degrees, but for some
    // devices it is 270 degrees. For devices with a sensor orientation of
    // 270, rotate the image an additional 180 ((270 + 270) % 360) degrees.
    val cameraManager = context.getSystemService(CAMERA_SERVICE) as CameraManager
    val sensorOrientation = cameraManager
            .getCameraCharacteristics(cameraId)
            .get(CameraCharacteristics.SENSOR_ORIENTATION)!!
    rotationCompensation = (rotationCompensation + sensorOrientation + 270) % 360

    // Return the corresponding FirebaseVisionImageMetadata rotation value.
    val result: Int
    when (rotationCompensation) {
        0 -> result = FirebaseVisionImageMetadata.ROTATION_0
        90 -> result = FirebaseVisionImageMetadata.ROTATION_90
        180 -> result = FirebaseVisionImageMetadata.ROTATION_180
        270 -> result = FirebaseVisionImageMetadata.ROTATION_270
        else -> {
            result = FirebaseVisionImageMetadata.ROTATION_0
            Log.e(TAG, "Bad rotation value: $rotationCompensation")
        }
    }
    return result
}
```

Then, pass the `media.Image` object and the rotation value to `FirebaseVisionImage.fromMediaImage()`:

* Java

```
FirebaseVisionImage image = FirebaseVisionImage.fromMediaImage(mediaImage, rotation);
```

* Kotlin

```
val image = FirebaseVisionImage.fromMediaImage(mediaImage, rotation)
```

To create a FirebaseVisionImage object from a file URI, pass the app context and file URI to FirebaseVisionImage.fromFilePath(). This is useful when you use an `ACTION_GET_CONTENT` intent to prompt the user to select an image from their gallery app.

* Java

```
FirebaseVisionImage image;
try {
    image = FirebaseVisionImage.fromFilePath(context, uri);
} catch (IOException e) {
    e.printStackTrace();
}
```

* Kotlin

```
val image: FirebaseVisionImage
try {
    image = FirebaseVisionImage.fromFilePath(context, uri)
} catch (e: IOException) {
    e.printStackTrace()
}
```

To create a `FirebaseVisionImage` object from a ByteBuffer or a byte array, first calculate the image rotation as described above for `media.Image` input.

Then, create a FirebaseVisionImageMetadata object that contains the image's height, width, color encoding format, and rotation:

* Java

```
FirebaseVisionImageMetadata metadata = new FirebaseVisionImageMetadata.Builder()
        .setWidth(480)   // 480x360 is typically sufficient for
        .setHeight(360)  // image recognition
        .setFormat(FirebaseVisionImageMetadata.IMAGE_FORMAT_NV21)
        .setRotation(rotation)
        .build();
```

* Kotlin

```
val metadata = FirebaseVisionImageMetadata.Builder()
        .setWidth(480) // 480x360 is typically sufficient for
        .setHeight(360) // image recognition
        .setFormat(FirebaseVisionImageMetadata.IMAGE_FORMAT_NV21)
        .setRotation(rotation)
        .build()
```
Use the buffer or array, and the metadata object, to create a FirebaseVisionImage object:

* Java

```
FirebaseVisionImage image = FirebaseVisionImage.fromByteBuffer(buffer, metadata);
// Or: FirebaseVisionImage image = FirebaseVisionImage.fromByteArray(byteArray, metadata);
```

* Kotlin


      val image = FirebaseVisionImage.fromByteBuffer(buffer, metadata)
      // Or: val image = FirebaseVisionImage.fromByteArray(byteArray, metadata)

To create a `FirebaseVisionImage` object from a Bitmap object:

* Java

    FirebaseVisionImage image = FirebaseVisionImage.fromBitmap(bitmap);

* Kotlin
    
    val image = FirebaseVisionImage.fromBitmap(bitmap)
    

The image represented by the Bitmap object must be upright, with no additional rotation required.

Get an instance of `FirebaseVisionFaceDetector`:

* Java

```
val detector = FirebaseVision.getInstance()
        .getVisionFaceDetector(options)
```

* Kotlin

      val detector = FirebaseVision.getInstance()
              .getVisionFaceDetector(options)

Note: Check the console for errors generated by the constructor.

Finally, pass the image to the detectInImage method:

* Java


      val result = detector.detectInImage(image)
              .addOnSuccessListener { faces ->
                  // Task completed successfully
                  // ...
              }
              .addOnFailureListener { e ->
                  // Task failed with an exception
                  // ...
              }            

* Kotlin

      val result = detector.detectInImage(image)
              .addOnSuccessListener { faces ->
                  // Task completed successfully
                  // ...
              }
              .addOnFailureListener { e ->
                  // Task failed with an exception
                  // ...
              }

3. Get information about detected faces

If the face recognition operation succeeds, a list of FirebaseVisionFace objects will be passed to the success listener. Each FirebaseVisionFace object represents a face that was detected in the image. For each face, you can get its bounding coordinates in the input image, as well as any other information you configured the face detector to find. For example:

* Java

      for (FirebaseVisionFace face : faces) {
          Rect bounds = face.getBoundingBox();
          float rotY = face.getHeadEulerAngleY();  // Head is rotated to the right rotY degrees
          float rotZ = face.getHeadEulerAngleZ();  // Head is tilted sideways rotZ degrees

          // If landmark detection was enabled (mouth, ears, eyes, cheeks, and
          // nose available):
          FirebaseVisionFaceLandmark leftEar = face.getLandmark(FirebaseVisionFaceLandmark.LEFT_EAR);
          if (leftEar != null) {
              FirebaseVisionPoint leftEarPos = leftEar.getPosition();
          }

          // If contour detection was enabled:
          List<FirebaseVisionPoint> leftEyeContour =
                  face.getContour(FirebaseVisionFaceContour.LEFT_EYE).getPoints();
          List<FirebaseVisionPoint> upperLipBottomContour =
                  face.getContour(FirebaseVisionFaceContour.UPPER_LIP_BOTTOM).getPoints();

          // If classification was enabled:
          if (face.getSmilingProbability() != FirebaseVisionFace.UNCOMPUTED_PROBABILITY) {
              float smileProb = face.getSmilingProbability();
          }
          if (face.getRightEyeOpenProbability() != FirebaseVisionFace.UNCOMPUTED_PROBABILITY) {
              float rightEyeOpenProb = face.getRightEyeOpenProbability();
          }

          // If face tracking was enabled:
          if (face.getTrackingId() != FirebaseVisionFace.INVALID_ID) {
              int id = face.getTrackingId();
          }
      }
      
* Kotlin

      for (face in faces) {
          val bounds = face.boundingBox
          val rotY = face.headEulerAngleY // Head is rotated to the right rotY degrees
          val rotZ = face.headEulerAngleZ // Head is tilted sideways rotZ degrees

          // If landmark detection was enabled (mouth, ears, eyes, cheeks, and
          // nose available):
          val leftEar = face.getLandmark(FirebaseVisionFaceLandmark.LEFT_EAR)
          leftEar?.let {
              val leftEarPos = leftEar.position
          }

          // If contour detection was enabled:
          val leftEyeContour = face.getContour(FirebaseVisionFaceContour.LEFT_EYE).points
          val upperLipBottomContour = face.getContour(FirebaseVisionFaceContour.UPPER_LIP_BOTTOM).points

          // If classification was enabled:
          if (face.smilingProbability != FirebaseVisionFace.UNCOMPUTED_PROBABILITY) {
              val smileProb = face.smilingProbability
          }
          if (face.rightEyeOpenProbability != FirebaseVisionFace.UNCOMPUTED_PROBABILITY) {
              val rightEyeOpenProb = face.rightEyeOpenProbability
          }

          // If face tracking was enabled:
          if (face.trackingId != FirebaseVisionFace.INVALID_ID) {
              val id = face.trackingId
          }
      }
      
 ### Examples
 
 1. Countour detection-
 
 ![](https://github.com/Rishit-dagli/MLKit-Firebase/blob/master/images/face_contours.svg)

2. Face + Emotion detection -

![](https://github.com/Rishit-dagli/MLKit-Firebase/blob/master/images/face%20detection%20example.jpeg)

## What next! 

You can contribute to this project, all you need to do is submit a pull request. Be sure to first read [CONTRIBUTING.md](https://github.com/Rishit-dagli/MLKit-Firebase-Android/blob/master/CONTRIBUTING.md).
