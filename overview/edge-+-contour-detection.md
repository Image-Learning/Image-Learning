# 🔳 Edge + Contour Detection

{% hint style="info" %}
**Tip:** There's python and Java code, If you wanna try it out on your computer or webcam I'd recommend trying it out on python, If you're learning for FTC or just want a quick implementation go for Java
{% endhint %}

## Video overview

Heres a demonstration: (ADD STUFF LATER; LIKE IMAGES / VID)

{% embed url="https://www.loom.com/embed/3bfa83acc9fd41b7b98b803ba9197d90" %}

## Basics

* Edge detection takes an image as input and returns another image that only has edges in it.
* Contour refers to a shape or an object ( which is detected as a connected set of edges)
* Contour Detection takes an image as input ( usually an “Edged” image) and returns a list of images, one for each contour.&#x20;
* Contour Detection also has additional intelligence to create hierarchy between external and internal contour

## Code (Java)

```java
import org.opencv.core.MatOfPoint;
import org.opencv.core.Moments;
import org.opencv.imgproc.Imgproc;
import java.util.ArrayList;
import java.util.List;
```

Here are the main import statements you'll need for just contours: **MatOfPoint** is used to store contours, **Moments** is for calculating the moments of the contour (coords), and Lastly Imgproc is for various image processing functions to get a specific contour we want.

```java
```

