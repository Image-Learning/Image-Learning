# 📸 Detection

{% hint style="info" %}
**Tip:** There's python and Java code. If you wanna try it out on your computer or webcam, I'd recommend trying it out on python. If you're learning for FTC or just want a quick implementation, go for Java.
{% endhint %}

## Video overview

Here's a demonstration:

{% embed url="https://www.loom.com/share/bad43cf95a464a889d7a957a1d0249f3?sid=f8c8597a-4697-4e36-b57b-4610987ffd9c" %}

## Basics

* Edge detection takes an image as input and returns another image that only has edges in it.
* Contour refers to a shape or an object ( which is detected as a connected set of edges)
* Contour Detection takes an image as input ( usually an “Edged” image) and returns a list of images, one for each contour.&#x20;
* Contour Detection also has additional intelligence to create hierarchy between external and internal contour

## Code (Java)

### The Start (imports)

<pre class="language-java"><code class="lang-java">/* Remember to change this depending on the location of this file */
<strong>package org.firstinspires.ftc.teamcode.auto.vision.teamprop;
</strong>
import androidx.annotation.GuardedBy;

import org.firstinspires.ftc.teamcode.auto.vision.PhysicalCamera;
import org.firstinspires.ftc.teamcode.auto.vision.VisionPipeline;
import org.firstinspires.ftc.teamcode.auto.vision.util.CameraCalibration;
import org.firstinspires.ftc.teamcode.common.ExecutionEnvironment;
import org.firstinspires.ftc.teamcode.common.debug.telemetry.TelemetryData;
import org.opencv.core.Core;
import org.opencv.core.Mat;
import org.opencv.core.MatOfPoint;
import org.opencv.core.Point;
import org.opencv.core.Scalar;
import org.opencv.imgproc.Imgproc;
import org.opencv.imgproc.Moments;

import java.util.ArrayList;
import java.util.List;

/* These imports are respective to our code this season, dont worry about these,
this is just how we adapted this project into our auto op mode */
import io.github.radrobotics.game.Alliance;
import io.github.radrobotics.game.SpikeStrip;
import io.github.radrobotics.game.StartPosition;
</code></pre>

Here are the main import statements you'll need for just contours: **MatOfPoint** is used to store contours, **Moments** is for calculating the moments of the contour (coords), and Lastly **Imgproc** is for various image processing functions to get a specific contour we want.

### Variables

<pre class="language-java"><code class="lang-java">public class TeamPropDetector extends VisionPipeline {

<strong>    /* These variables are respective to our code this season, dont worry about
</strong><strong>    these, this is just how we adapted this project into our auto op mode */
</strong>    private final Object lock = new Object();
    private Alliance alliance = Alliance.BLUE;
    private SpikeStrip detectionLeft = SpikeStrip.LEFT;       
    private SpikeStrip detectionRight = SpikeStrip.CENTER;    
    private SpikeStrip detectionNone = SpikeStrip.RIGHT;      
    
    /* The things called "Mats" are what we use to find certain objects through 
    their respective colours (red &#x26; blue), sizes and coordinates (frame) */
    private final Mat bgr = new Mat();
    private final Mat undistorted = new Mat();
    private final Mat mHsv = new Mat();
    private final Mat blue_mask = new Mat();
    private final Mat red_mask = new Mat();
    private final Mat lower_red_mask = new Mat();
    private final Mat upper_red_mask = new Mat();
    private Mat mask = null;
    private final Mat result = new Mat();
    private final Mat frameCopyAll = new Mat();
    private final Mat frameCopyBig = new Mat();
    private final Mat frameCopyBigRgba = new Mat();
    
    /* We had multiple cameras on the robot, so we didn't want them all streaming
    at once, so we decided to alternate when they would be used */
    private boolean isEnabled = false;

</code></pre>

### Camera Handler

```java
    @GuardedBy("lock")
    private SpikeStrip spikeStrip = null;

    /* These conditionsare respective to our code this season, dont worry about 
    these, this is just how we adapted this project into our auto op mode */
    public void initDetection(StartPosition startPos) {
        this.alliance = startPos.alliance;
        // TODO: v2 specific hackish code :(
        if (startPos == StartPosition.A2 || startPos == StartPosition.F4) {
            this.detectionLeft = SpikeStrip.CENTER;
            this.detectionRight = SpikeStrip.RIGHT;
            this.detectionNone = SpikeStrip.LEFT;
        } else if (startPos == StartPosition.A4 || startPos == StartPosition.F2) {
            this.detectionLeft = SpikeStrip.LEFT;
            this.detectionRight = SpikeStrip.CENTER;
            this.detectionNone = SpikeStrip.RIGHT;
        }
    }

    public boolean isEnabled() {
        return this.isEnabled;
    }

    /* The statements below are just to handle what happens to the camera b4 and 
    after detection, such as shutting of the camera after detection or init */
    @Override
    public void onEnable(PhysicalCamera camera) {
        synchronized (this.lock) {
            if (this.isEnabled) {
                throw new IllegalStateException("TeamPropDetector is already enabled");
            }

            this.isEnabled = true;
            camera.setDefaultExposure();
        }
    }

    @Override
    public void onDisable(PhysicalCamera camera) {
        synchronized (this.lock) {
            if (!this.isEnabled) {
                throw new IllegalStateException("TeamPropDetector is already disabled");
            }

            this.isEnabled = false;
        }
    }

    public SpikeStrip awaitDetection() throws InterruptedException {
        synchronized (this.lock) {
            if (!this.isEnabled) {
                throw new IllegalStateException("TeamPropDetector is not enabled");
            }

            while (this.spikeStrip == null) {
                this.lock.wait();
            }
            return this.spikeStrip;
        }
    }
```

Again, this code is just our implementation of this detection, and it's for disabling a camera stream after initialization, or unless used again. This just allows us to keep our looptimes down, and you're welcome to use it if you need.

### HSV + Value filters

```java
    
    
    /* These are just the bounds we use to narrow down what our desired object is */
    @Override
    public Mat processFrame(Mat frame) {
        Imgproc.cvtColor(frame, this.bgr, Imgproc.COLOR_RGBA2BGR);

        CameraCalibration.undistort(this.bgr, this.undistorted);
        Imgproc.cvtColor(undistorted, mHsv, Imgproc.COLOR_BGR2HSV);
        
        
        
        Scalar lowerBlue = new Scalar(100, 100, 63);
        Scalar upperBlue = new Scalar(130, 255, 255);

        // lower boundary RED color range values; Hue (0 - 10)
        Scalar lowerRed1 = new Scalar(0, 100, 20);
        Scalar upperRed1 = new Scalar(10, 255, 255);

        // upper boundary RED color range values; Hue (160 - 180)
        Scalar lowerRed2 = new Scalar(160,100,20);
        Scalar upperRed2 = new Scalar(179,255,255);
        
        /* This is our implementation of looking for either a blue or red object */
        List<MatOfPoint> contours = new ArrayList<>();
        Mat hierarchy = new Mat();
        if (alliance == Alliance.BLUE) {
            Core.inRange(mHsv, lowerBlue, upperBlue, blue_mask);
            mask = blue_mask;
        }
        else {
            Core.inRange(mHsv, lowerRed1, upperRed1, lower_red_mask);
            Core.inRange(mHsv, lowerRed2, upperRed2, upper_red_mask);
            Core.bitwise_or(lower_red_mask, upper_red_mask, red_mask);
            mask = red_mask;
        }
        
        
        Core.bitwise_and(undistorted, undistorted, result, mask);

        Imgproc.findContours(mask, contours, hierarchy, Imgproc.RETR_LIST, Imgproc.CHAIN_APPROX_SIMPLE);
        
        /* If for some reason, we need to switch objects or lighting is bad, we can use this to debug */
        if (ExecutionEnvironment.DEBUG) {
            undistorted.copyTo(frameCopyAll);
            undistorted.copyTo(frameCopyBig);
            System.out.println("number of contours before filtering: " + contours.size());
        }
```

Use this page for Saturation and Value:&#x20;

{% embed url="https://colorpicker.me/#4906fb" %}
This above page provides percentage. To use in openCV multiply % by 255 (max val is 255)
{% endembed %}

For Hue use this page: &#x20;

{% embed url="https://cvexplained.wordpress.com/2020/04/28/color-detection-hsv/" %}
This is in python but it can still help you understand our usage
{% endembed %}

### Area + Coords

This huge chunk of code may seem **daunting**, but it just looks that way. We can split this code up into 2 parts; The first being the moments library which basically helps us understand the frame in which the object is in using a coordinate type system, and the last part in narrowing down what are object is. In essence, moments helps us find the _where_ and HSV, Value and Area help us find the _what_.

```java
        double maxArea = -9999999999.0;
        SpikeStrip strip = this.detectionNone;

        /* This first but has the moments library which finds the coords of the object */
        List<MatOfPoint> bigCnt = new ArrayList<>();
        for (int i = 0; i < contours.size(); i++) {
            // TODO: at the moment, this leaks memory since contour is not guaranteed to be added
            //  to bigCnt, so it might not be released.
            MatOfPoint contour = new MatOfPoint(contours.get(i));
            double area = Imgproc.contourArea(contour, false);
            if (ExecutionEnvironment.DEBUG) System.out.println("Area: " + area);
            
            if (area < 5000) continue;
            
            Moments moments = Imgproc.moments(contour);
            double m00 = moments.get_m00();
            if (m00 == 0) continue;
            
            int cX = (int) (moments.get_m10() / m00);
            int cY = (int) (moments.get_m01() / m00);
            SpikeStrip detection = this.detectionNone;
            
            /* This looks what frame the object is in, so it can be classified w/ a spike strip */
            if (cX >= 0 && cX <= 320) {
                detection = this.detectionLeft;
            } else if (cX >= 320 && cX <= 640) {
                detection = this.detectionRight;
            }
            /* If you're wondering why there aren't 3 conditions, it's b/c one is default */
            
            /* Just like w/ HSV we have a high & low bounds for area */
            double areaLow = 7500;
            if (detection == SpikeStrip.CENTER) {
                areaLow = 5000;
            }
            
            /* We put a final contour around just our specefied item */            
            if (area >= areaLow && area >= maxArea) {
                maxArea = area;
                bigCnt.add(contour);
                if (ExecutionEnvironment.DEBUG) System.out.println(area + " " + cX + " " + cY);
                strip = detection;
                
                /* We can then overlay the coords over the object */
                if (ExecutionEnvironment.DEBUG) {
                    String coord = cX + " " + cY + " " + area;
                    Imgproc.putText(frameCopyBig, coord, new Point(cX, cY),
                            Imgproc.FONT_HERSHEY_COMPLEX, 1, new Scalar(8, 232, 222));
                }
            }
        }
        
        /* We can also overlay which spike strip the object is on over the object+contour */
        if (ExecutionEnvironment.DEBUG) {
            Imgproc.putText(frameCopyBig, String.valueOf(strip), new Point(375, 375), Imgproc.FONT_HERSHEY_COMPLEX, 1, new Scalar(255, 0, 127));
        }

        synchronized (this.lock) {
            this.spikeStrip = strip;
            this.lock.notifyAll();
        }
        
        /* Lastly we can show the windows themselves */
        if (ExecutionEnvironment.DEBUG) {
            System.out.println("number of contours after filtering: " + bigCnt.size());
            Imgproc.drawContours(frameCopyAll, contours, -1, new Scalar(0, 255, 0), 2);
            Imgproc.drawContours(frameCopyBig, bigCnt, -1, new Scalar(8, 232, 222), 2);
        }
```

### Closing

```java
        /* We can also release the frames and these mats */
        contours.forEach(MatOfPoint::release);
        bigCnt.forEach(MatOfPoint::release);
        hierarchy.release();

        //  return frame;
        //  TODO: for some reason, returning frameCopyBigRgba causes an error because it is an "empty
        //  mat". Don't know why this is happening, but not very important for now since this is
        //  just for debugging.
        if (ExecutionEnvironment.DEBUG) {
            Imgproc.cvtColor(frameCopyBig, frameCopyBigRgba, Imgproc.COLOR_BGR2RGBA);
            return frameCopyBigRgba;
        } else {
            return frame;
        }
    }
    
    /* This updates the telemetry (we have this show up in auto), this is just our implementation */
    @Override
    public void updateTelemetry() {
        TelemetryData.addData("Spike Strip", this.spikeStrip == null ? "Not detected" : this.spikeStrip);
    }
    
    /* We can also release the frames and these mats */
    @Override
    public void destroy() {
        this.bgr.release();
        this.undistorted.release();
        this.mHsv.release();
        this.blue_mask.release();
        this.red_mask.release();
        this.upper_red_mask.release();
        this.lower_red_mask.release();
        this.result.release();
        this.frameCopyAll.release();
        this.frameCopyBig.release();
        this.frameCopyBigRgba.release();
    }
}
```
