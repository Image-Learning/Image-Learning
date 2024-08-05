---
description: Created by Amitesh_Sen | Green.Griffins and Aarsh | Voyager 6+
---

# 👋 Welcome to Java EOCV

{% hint style="info" %}
**Tip:** If you have any questions feel free to DM s1ug\_ or !!!! or lunbun\_ on discord!
{% endhint %}

## Overview

EOCV stands for "Easy Open CV". It's main purpose is to allow teams, from rookies to worlds-winners, to use OpenCV effectively on an FTC robot! But what is OpenCV? OpenCV is an open-source computer vision library that provides tools and functions for image and video processing, enabling applications like facial recognition, object detection, and more. To learn about EOCV, visit its [GitHub](https://github.com/OpenFTC/EasyOpenCV), and to learn more about OpenCV visit their [official website](https://opencv.org/).

This notebook goes over the basics of EOCV and some applications/uses of features with one big project. This all encompassing project includes Edge+Contour Detection, HSV and Value Filters, and lastly Object Detection and matching. Each piece of the puzzles is explained in this overview of the project.

## Prerequisites:

You will want to have Android Studio installed and the FTC SDK downloaded in order to properly use EOCV for FTC. Moreover, a basic understanding of Java will be helpful but not required as EOCV is Java based when using it for FTC. We also have a separate section for Python which requires nothing else but a Python IDE installed.&#x20;

### Vision Pipeline

We have an abstract class called VisionPipeline which we will use in this project. It is highly recommended that you copy this code as our example code will be built on this.&#x20;

<pre class="language-java"><code class="lang-java"><strong>/** Remember to change this depending on the location of this file */
</strong><strong>package org.firstinspires.ftc.teamcode.auto.vision;
</strong>
import org.openftc.easyopencv.OpenCvPipeline;

public abstract class VisionPipeline extends OpenCvPipeline {
    /**
     * Called when the pipeline is enabled. This is a good place to reset any 
     * state that should be cleared when the camera is (re)started, for example, 
     * the positions of detected objects.
     */
    public void onEnable(PhysicalCamera camera) {
    }

    /**
     * Called when the pipeline is disabled. This is a good place to clean up any 
     * resources that were allocated in onEnable.
     */
    public void onDisable(PhysicalCamera camera) {
    }

    /**
     * Updates the telemetry with the latest information from the pipeline. 
     * This will be called from the main thread, so it is safe to update the 
     * telemetry here (however it is the responsibility of the pipeline to 
     * ensure reading its own state is thread-safe).
     */
    public void updateTelemetry() {
    }

    /**
     * Destroys the pipeline and frees up resources.
     */
    public void destroy() {
    }
}
</code></pre>

## FTC (Java)

{% content-ref url="java/edge-+-contour-detection.md" %}
[edge-+-contour-detection.md](java/edge-+-contour-detection.md)
{% endcontent-ref %}

{% content-ref url="java/our-features.md" %}
[our-features.md](java/our-features.md)
{% endcontent-ref %}

## Python

We've put together some helpful guides for you to get setup with our product quickly and easily.

{% content-ref url="python/getting-set-up/" %}
[getting-set-up](python/getting-set-up/)
{% endcontent-ref %}

{% content-ref url="python/getting-set-up/setting-permissions.md" %}
[setting-permissions.md](python/getting-set-up/setting-permissions.md)
{% endcontent-ref %}

{% content-ref url="python/getting-set-up/inviting-members.md" %}
[inviting-members.md](python/getting-set-up/inviting-members.md)
{% endcontent-ref %}
