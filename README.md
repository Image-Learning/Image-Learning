---
description: Created by Amitesh_Sen | Green.Griffins;
---

# 👋 Welcome to Java EOCV

{% hint style="info" %}
**Tip:** If you have any questions feel free to DM s1ug\_ or lunbun\_ on discord!
{% endhint %}

## Overview

This notebook goes over the basics of EOCV and some applications/uses of features with one big project. This all encompassing project includes Edge+Contour Detection, HSV and Value Filters, and lastly Object Detection and matching. Each piece of the puzzles is explained in this overview of the project.

## Prerequisites:

You want to have Android Studio Installed and the FTC SDK downloaded, in order to try this project for FTC. Otherwise we have a seperate Section for Python which requires nothing else but a Python IDE installed.&#x20;

### Vision Pipeline

We have an abstract class called VisionPipeline that we use in this project that you should copy:

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

{% content-ref url="overview/edge-+-contour-detection.md" %}
[edge-+-contour-detection.md](overview/edge-+-contour-detection.md)
{% endcontent-ref %}

{% content-ref url="overview/our-features.md" %}
[our-features.md](overview/our-features.md)
{% endcontent-ref %}

## Python

We've put together some helpful guides for you to get setup with our product quickly and easily.

{% content-ref url="fundamentals/getting-set-up/" %}
[getting-set-up](fundamentals/getting-set-up/)
{% endcontent-ref %}

{% content-ref url="fundamentals/getting-set-up/setting-permissions.md" %}
[setting-permissions.md](fundamentals/getting-set-up/setting-permissions.md)
{% endcontent-ref %}

{% content-ref url="fundamentals/getting-set-up/inviting-members.md" %}
[inviting-members.md](fundamentals/getting-set-up/inviting-members.md)
{% endcontent-ref %}
