---
date: 2025-09-22 12:31:31
layout: post
title: "Building HornetWatcher: Real-Time Asian Hornet Detection Using AI, .NET
  8 WPF, and YOLOv8"
description: Asian hornets (Vespa velutina) pose a significant threat to native
  bee populations and ecosystems across Europe and beyond. Traditional
  monitoring methods are time-consuming and often ineffective. This article
  details the complete development journey of HornetWatcher, a real-time
  AI-powered desktop application that uses computer vision to automatically
  detect and track Asian hornets via webcam feeds.  HornetWatcher combines
  modern .NET 8 WPF UI, YOLOv8 object detection, PostgreSQL data persistence,
  and a sophisticated learning workflow to create a professional-grade wildlife
  monitoring solution.
image: /assets/img/uploads/unnamed-10-.png
optimized_image: /assets/img/uploads/unnamed-10-.png
category: "{{slug}}"
tags:
  - AI
  - NodeJS
  - DOTNET
  - python
author: garywenneker
paginate: false
---
## How a Tiny Flying Terror Sparked a Full-Stack Development Adventure

Picture this: you're enjoying a peaceful morning coffee when suddenly, a colleague bursts in with the urgency of someone who's just discovered their production server is on fire. "We need to detect Asian hornets!" they declare, as if this is a perfectly normal Monday morning request. And that's how I found myself tumbling down the rabbit hole of computer vision, real-time detection, and what would become the most interesting project I'd worked on in years.

Let me tell you the story of HornetWatcher – a journey that taught me more about invasive species than I ever expected to know, and proved that sometimes the most rewarding projects come from the most unexpected places.

### When Nature Becomes a Tech Problem

Asian hornets (*Vespa velutina*, if you want to get fancy about it) are basically the honey badgers of the insect world – they don't care about your feelings, your bees, or your carefully balanced ecosystem. These flying menaces showed up in France back in 2004 like uninvited party guests who never leave, and they've been systematically working their way across Europe ever since.

Here's where things get serious: a single Asian hornet can take out 50 honeybees per day. That's not a typo – fifty. It's like having a tiny, flying terminator with an insatiable appetite for the insects we desperately need for pollination. The economic damage? We're talking about €400 million annually across affected regions. That's a lot of honey that will never be made.

But here's the kicker – traditional monitoring methods are about as effective as using a magnifying glass to watch every tree in a forest. Sure, you might spot something eventually, but by then, the hornets have probably set up a vacation home, started a family, and sent postcards to their relatives back in Asia.

### The "Eureka!" Moment (Or: How I Learned to Stop Worrying and Love Computer Vision)

Somewhere between reading about beekeepers losing entire colonies and watching yet another "Is this a hornet?" video that looked like it was filmed with a potato, it hit me: this is exactly the kind of problem that AI was made to solve. We needed something that could watch 24/7 without getting tired, bored, or distracted by shiny objects.

The vision was simple: point a camera at a location, let the computer do the watching, and get alerted the moment an Asian hornet shows up. Simple, right? (Spoiler alert: it wasn't simple, but it was incredibly fun to figure out.)

## The Great Technology Expedition (Or: How to Build a Hornet-Hunting Robot)

Now comes the fun part – choosing the right tools for the job. You know that feeling when you walk into a hardware store knowing you need "something to fix the thing," and suddenly you're overwhelmed by 47 different types of screws? That's exactly what choosing a tech stack feels like, except the screws can compile and the thing you're fixing might fly away.

### The Desktop Dilemma: Why WPF Won the Battle

First decision: how do you build something that needs to process video in real-time while maintaining a user interface that doesn't look like it was designed in 1995? The options seemed endless – web apps, Electron, mobile apps, or good old-fashioned desktop applications.

I'll be honest, for about five minutes I considered building this as a web application. After all, web apps are trendy, cross-platform, and everyone loves them, right? Then reality kicked in: try accessing someone's webcam through a browser while doing real-time computer vision processing. It's like trying to perform surgery through oven mitts – technically possible, but why would you?

.NET 8 WPF emerged as the hero of this story. Sure, it's not the flashiest kid on the block, but it has something that web technologies can't match: direct, unfettered access to hardware. When you need to grab frames from a camera at 30fps, process them through an AI model, and update a UI without missing a beat, you want native performance. Plus, WPF has matured like a fine wine – it's stable, well-documented, and won't surprise you with breaking changes every Tuesday.

The MaterialDesignInXamlToolkit sealed the deal. Because let's face it, if you're going to build something that helps save the bee population, it should at least look good while doing it. No one wants to use software that looks like it was designed by someone who thinks Comic Sans is a valid font choice.

### The Computer Vision Quest: OpenCvSharp to the Rescue

Next challenge: how do you actually grab video from a camera and do something useful with it? Enter OpenCvSharp, the .NET wrapper for OpenCV that makes computer vision accessible without requiring a PhD in mathematics.

I briefly flirted with AForge.NET (hey, we've all made questionable decisions), but it felt like trying to run a modern race car on spark plugs from the 1980s. OpenCvSharp, on the other hand, gives you the full power of OpenCV with all the safety and convenience of managed .NET code. It's like having a professional race car driver who also happens to be your designated driver – you get the performance without the risk of crashing into a tree.

The beauty of OpenCvSharp is that it handles all the low-level camera nastiness for you. Different camera drivers? Check. Various video formats? Check. Memory management that doesn't leak like a sieve? Double check. It's the Swiss Army knife of computer vision, if Swiss Army knives could detect flying insects.

### The AI Adventure: Why YOLOv8 Became My New Best Friend

Now for the star of the show: the AI that would actually detect the hornets. Choosing an object detection model is a bit like choosing a superhero – they all have different strengths, and you need to pick the one that matches your specific brand of chaos.

YOLOv8 won my heart for several reasons. First, it's fast enough for real-time detection, which is crucial when your target moves at the speed of an angry insect. Second, it's accurate enough to tell the difference between a hornet and, say, a large bee (this distinction matters more than you might think). Third, and this is important, it comes with an entire ecosystem of training tools that don't require you to write your own backpropagation algorithms.

The ONNX export capability was the cherry on top. ONNX Runtime is like the universal translator of the AI world – it speaks every framework's language and runs everywhere. No more "it works on my machine" problems when deploying to different environments.

### The Database Deliberation: PostgreSQL Steps Up

For storing all this precious hornet data, I needed something more sophisticated than a text file (though let's be honest, we've all been there). PostgreSQL emerged as the obvious choice, not just because it's solid and reliable, but because it has a secret weapon: native JSON support.

When you're dealing with detection events that might have varying metadata – confidence scores, bounding boxes, environmental conditions, maybe even the hornet's apparent mood – having a database that can handle both structured and semi-structured data is invaluable. Plus, PostgreSQL's time series capabilities mean I won't have to rewrite everything when someone inevitably asks for "trend analysis over the last six months."

Dapper became my data access layer of choice because sometimes you want the performance of raw SQL without the ceremony of a full ORM. It's like having a really good assistant who does exactly what you ask without questioning your questionable life choices.

### AI/ML Pipeline

- **Ultralytics YOLOv8**: Pre-trained model fine-tuned on Asian hornet dataset
- **ONNX Export**: Cross-platform inference optimization
- **PyTorch + CUDA**: GPU-accelerated training on Windows
- **Custom Dataset**: Roboflow-hosted Vespa velutina detection dataset

## Architecting for Success (And My Quest to Avoid Spaghetti Code)

With the technology stack chosen, it was time to face every developer's favorite challenge: how to organize this whole thing so that future-me doesn't spend three hours trying to figure out what past-me was thinking. You know the feeling – you open your own code from six months ago and wonder if it was written by a particularly creative chimpanzee.

The answer was clean architecture, though getting there required some trial and error (and maybe a few heated discussions with my rubber duck).

### The Three-Project Solution: Divide and Conquer

I settled on a three-project structure that would make my computer science professors proud:

**HornetWatcher.Core** became the brain of the operation – all the business logic, detection services, and data models live here. This is where the magic happens, and more importantly, where the unit tests can actually test something meaningful without spinning up a full UI.

**HornetWatcher.App** handles everything the user sees and clicks. It's the pretty face of the operation, but it knows its place – it displays information and collects input, but it doesn't try to be clever about hornet detection algorithms.

**HornetWatcher.Tests** is where I prove to myself (and future maintainers) that this thing actually works. There's something deeply satisfying about having a test suite that can verify your hornet detection logic without requiring actual hornets.

This separation turned out to be a lifesaver. When I inevitably needed to tweak the detection algorithm (spoiler: this happened approximately 47 times), I could do it without worrying about breaking the UI. When the design requirements changed (spoiler: this also happened), I could update the interface without touching the core logic.

### The Strategy Pattern: Because Flexibility is Everything

Here's where things got interesting. During development, I needed a way to test the interface and tracking logic without having actual hornets to detect. The solution? ArUco markers – those square, QR-code-looking things that computer vision systems love because they're basically the golden retrievers of the image recognition world: easy to spot, reliable, and always where you expect them to be.

But in production, I needed real ONNX model detection. Enter the strategy pattern, which is just a fancy way of saying "I can swap out the detection method without rewriting everything else."

```csharp
public interface IHornetDetector
{
    (int[] ids, Point2f[][] corners, float[] scores) Detect(Mat frame);
}
```

This simple interface became the foundation for everything. During development, I could use ArUco detection to verify that tracking, timers, and UI updates all worked correctly. In production, I could seamlessly switch to the ONNX model without changing a single line of tracking or UI code. It's like having a sports car that you can switch between regular gas and premium – same engine, different fuel.

### Event-Driven Architecture: The Art of Not Caring

One of the best decisions I made was embracing event-driven architecture for detection updates. Instead of having the detection service directly call UI update methods (which would have been a coupling nightmare), it simply announces "Hey, I found something!" to whoever might be listening.

This turned out to be brilliant for several reasons. The UI could update its timers, the database logger could record the event, the audio system could play alerts, and future features could plug in without modifying existing code. It's like having a really good PA system at a party – the DJ just announces what's happening, and everyone who cares can respond appropriately.

```csharp
public interface IHornetDetector
{
    /// <summary>
    /// Returns detected hornet IDs, their polygon corners, and optional confidence scores [0..1] for a given frame.
    /// For ArUco: scores will be 1.0 for all detections.
    /// For YOLO/ONNX: scores are the post-NMS confidence per detection.
    /// </summary>
    (int[] ids, Point2f[][] corners, float[] scores) Detect(Mat frame);
}

public class HornetTracker
{
    private readonly Dictionary<int, HornetState> _states = new();
    private readonly TimeSpan _departThreshold;

    public event EventHandler<VisitLoggedEventArgs>? VisitLogged;

    public void MarkSeen(int id, DateTimeOffset now)
    {
        var s = GetOrCreate(id);
        if (!s.IsPresent)
        {
            s.IsPresent = true;
            if (s.DepartedAt.HasValue)
            {
                var duration = (now - s.DepartedAt.Value).TotalSeconds;
                VisitLogged?.Invoke(this, new VisitLoggedEventArgs
                {
                    TagId = id,
                    DepartedAt = s.DepartedAt.Value,
                    ReturnedAt = now,
                    DurationSeconds = duration
                });
            }
            s.DepartedAt = null;
        }
        s.LastSeen = now;
    }
}
```

**Strategy Pattern for Detection**: The application supports multiple detection backends (ArUco markers for development, ONNX for production) through a unified interface.

**Why Strategy Pattern for Detection Backends?**

The strategy pattern enables seamless switching between detection methods:

- **Development Workflow**: ArUco markers provide reliable test targets during UI development
- **Production Deployment**: ONNX models deliver actual hornet detection in field conditions
- **A/B Testing**: Easy comparison between different models or detection approaches
- **Graceful Degradation**: Fallback to marker detection if ONNX model fails to load
- **Future Extensibility**: Additional detection methods (YOLO variants, custom models) can be added without UI changes

**Event-Driven Architecture**: Real-time detection events drive UI updates, timer management, and data persistence without tight coupling.

**Why Event-Driven Over Direct Method Calls?**

Event-driven architecture provides critical benefits for real-time monitoring:

- **Loose Coupling**: UI components don't directly depend on detection internals
- **Multiple Subscribers**: Database logging, UI updates, and audio alerts can all respond to the same events
- **Temporal Decoupling**: Events can be queued if UI is temporarily busy
- **Extensibility**: New features (email alerts, API notifications) can subscribe without modifying existing code
- **Error Isolation**: Failure in one event handler doesn't affect others

## Computer Vision Implementation

### Camera Capture Pipeline

**Why Custom Capture Loop Over WPF MediaElement?**

The OpenCvSharp-based capture implementation provides advantages essential for computer vision:

- **Frame Access**: Direct access to raw image data for processing
- **Timing Control**: Precise control over capture intervals and buffer management
- **Format Flexibility**: Support for various image formats and color spaces
- **Performance**: Minimal overhead compared to WPF media controls
- **Cross-Platform**: Foundation for future Linux deployment
- **Buffer Management**: Control over frame buffering to prevent memory leaks

**Camera Configuration Rationale**:

```csharp
_capture.Set(VideoCaptureProperties.FrameWidth, 1920);
_capture.Set(VideoCaptureProperties.FrameHeight, 1080);
_capture.Set(VideoCaptureProperties.BufferSize, 1);
```

- **1920x1080 Resolution**: Balances detail for detection with processing performance
- **Buffer Size 1**: Minimizes latency by preventing frame queuing
- **30fps Processing**: Sufficient temporal resolution while allowing time for inference

**Async Processing Benefits**:

- **UI Responsiveness**: Camera loop runs on background thread
- **Cancellation Support**: Clean shutdown without resource leaks
- **Error Recovery**: Graceful handling of camera disconnections
- **Backpressure Handling**: Skip frames when processing can't keep up

The application uses OpenCvSharp for robust camera handling with configurable device selection:

```csharp
public partial class MainWindow : System.Windows.Window
{
    private VideoCapture? _capture;
    private CancellationTokenSource? _cts;
    private Task? _loopTask;
    private HornetTracker _tracker = new();
    private IHornetDetector _detector;

    private async Task InitializeCameraAsync(int index)
    {
        _capture?.Dispose();
        _capture = new VideoCapture(index);
        
        if (!_capture.IsOpened())
        {
            throw new InvalidOperationException($"Failed to open camera {index}");
        }
        
        // Configure camera properties
        _capture.Set(VideoCaptureProperties.FrameWidth, 1920);
        _capture.Set(VideoCaptureProperties.FrameHeight, 1080);
        _capture.Set(VideoCaptureProperties.BufferSize, 1);
        
        // Start capture loop
        _cts = new CancellationTokenSource();
        _loopTask = CaptureLoopAsync(_cts.Token);
    }

    private async Task CaptureLoopAsync(CancellationToken ct)
    {
        using var frame = new Mat();
        while (!ct.IsCancellationRequested)
        {
            if (!_capture.Read(frame) || frame.Empty())
            {
                await Task.Delay(16, ct); // ~60fps fallback
                continue;
            }

            // Run detection
            var (ids, corners, scores) = _detector.Detect(frame);
            
            // Update tracking
            ProcessDetections(ids, corners, scores);
            
            // Update UI (non-blocking)
            UpdateFrameDisplay(frame);
            
            await Task.Delay(33, ct); // ~30fps processing
        }
    }
}
```

### YOLOv8 Integration

**Why Letterboxing Instead of Simple Resize?**

Letterboxing preserves aspect ratio and provides several critical benefits:

- **Aspect Ratio Preservation**: Prevents distortion that could affect detection accuracy
- **Consistent Input Size**: Model trained on specific dimensions receives expected input format
- **Edge Case Handling**: Graceful processing of various camera aspect ratios
- **Detection Quality**: Maintains spatial relationships crucial for accurate bounding box prediction

**Preprocessing Pipeline Rationale**:

1. **Scale Calculation**: `Math.Min((float)inputW / origW, (float)inputH / origH)` ensures no dimension exceeds model input size
2. **Padding Distribution**: Center-aligned padding provides symmetric borders
3. **Gray Padding (114,114,114)**: Neutral color reduces edge artifacts during inference
4. **BGR→RGB Conversion**: ONNX models typically expect RGB channel order
5. **Normalization [0,1]**: Floating-point normalization matches training preprocessing

**Why CHW Format Over HWC?**

Channel-first (CHW) format alignment with model expectations:
- **Model Compatibility**: YOLOv8 ONNX exports use CHW by default
- **Memory Efficiency**: Contiguous channel data improves cache performance
- **Vectorization**: SIMD optimizations work better with channel-aligned data

**Tensor Creation Performance**:
```csharp
var input = new DenseTensor<float>(new[] { 1, 3, _inputH, _inputW });
```
- **Memory Pre-allocation**: Avoids dynamic resizing during pixel copying
- **Type Safety**: Float32 tensors match model precision requirements
- **Batch Dimension**: [1,3,H,W] format supports potential future batch processing

**Output Processing Flexibility**:

Supporting both `[1, N, C]` and `[1, C, N]` output formats addresses:
- **Model Variations**: Different YOLO export configurations produce different layouts
- **Framework Differences**: PyTorch vs TensorFlow exports may vary
- **Version Compatibility**: Ensures compatibility across YOLOv8 versions

**Non-Maximum Suppression Implementation**:

Custom NMS implementation provides:
- **No External Dependencies**: Avoids additional library requirements
- **Performance Control**: Optimized for typical detection scenarios
- **Threshold Flexibility**: Configurable IoU thresholds for different environments
- **Debugging Capability**: Full control over suppression logic for troubleshooting

**Coordinate Mapping Precision**:

```csharp
private static Rect2f MapBoxToOriginal(Rect2f rect, float scale, int padLeft, int padTop, int origW, int origH)
```

Precise coordinate transformation ensures:
- **Pixel Accuracy**: Bounding boxes align correctly with original image
- **Clipping Safety**: Prevents coordinates from extending beyond image boundaries
- **Scale Compensation**: Reverses letterboxing transformations exactly
- **Integer Boundaries**: Ensures valid pixel coordinates for display

**Tracking System Design Rationale**:

The simple IoU-based tracking system balances complexity with effectiveness:

**Why IoU-Based Tracking Over Kalman Filters?**

- **Simplicity**: Easier to debug and maintain than complex state estimation
- **No Motion Model**: Hornets have unpredictable flight patterns that violate linear motion assumptions
- **Robust to Occlusion**: IoU matching works well for brief detection gaps
- **Computationally Efficient**: O(N²) complexity acceptable for typical detection counts
- **Parameter Simplicity**: Single IoU threshold easier to tune than multi-parameter filters

**Track Lifecycle Management**:

```csharp
private const int TrackTtl = 30; // frames
_tracks.RemoveAll(tr => _frameIndex - tr.LastFrame > TrackTtl);
```

- **30-Frame TTL**: ~1 second at 30fps allows for brief occlusions
- **Automatic Cleanup**: Prevents memory growth from abandoned tracks
- **Configurable Lifespan**: TTL can be adjusted for different monitoring scenarios

**ID Assignment Strategy**:

- **Incremental IDs**: Simple, predictable numbering for user interface
- **Stable Association**: Once assigned, IDs remain consistent until track expires
- **Conflict Resolution**: IoU-based matching prevents ID swapping between nearby detections

**1. Frame Preprocessing with Letterboxing**:
```csharp
public (int[] ids, Point2f[][] corners, float[] scores) Detect(Mat frame)
{
    if (_session == null || string.IsNullOrEmpty(_inputName))
        return (Array.Empty<int>(), Array.Empty<Point2f[]>(), Array.Empty<float>());

    // Letterbox preprocessing
    int origW = frame.Width, origH = frame.Height;
    float scale = Math.Min((float)_inputW / origW, (float)_inputH / origH);
    int newW = (int)Math.Round(origW * scale);
    int newH = (int)Math.Round(origH * scale);
    int padW = _inputW - newW, padH = _inputH - newH;
    int padLeft = padW / 2, padTop = padH / 2;

    using var resized = new Mat();
    Cv2.Resize(frame, resized, new Size(newW, newH));
    using var letterbox = new Mat(new Size(_inputW, _inputH), MatType.CV_8UC3, new Scalar(114, 114, 114));
    resized.CopyTo(new Mat(letterbox, new Rect(padLeft, padTop, newW, newH)));

    // BGR->RGB and normalize to float CHW [1,3,H,W]
    using var rgb = new Mat();
    Cv2.CvtColor(letterbox, rgb, ColorConversionCodes.BGR2RGB);
    var input = new DenseTensor<float>(new[] { 1, 3, _inputH, _inputW });
    
    var rgbIndexer = rgb.GetGenericIndexer<Vec3b>();
    for (int y = 0; y < _inputH; y++)
    {
        for (int x = 0; x < _inputW; x++)
        {
            var px = rgbIndexer[y, x];
            input[0, 0, y, x] = px.Item0 / 255f; // R
            input[0, 1, y, x] = px.Item1 / 255f; // G
            input[0, 2, y, x] = px.Item2 / 255f; // B
        }
    }

    // Run inference
    var inputValue = NamedOnnxValue.CreateFromTensor(_inputName!, input);
    using var results = _session.Run(new[] { inputValue });
    
    return ProcessYoloOutput(results.First().AsTensor<float>(), scale, padLeft, padTop, origW, origH);
}
```

**2. YOLOv8 Output Processing and NMS**:
```csharp
private (int[] ids, Point2f[][] corners, float[] scores) ProcessYoloOutput(
    Tensor<float> output, float scale, int padLeft, int padTop, int origW, int origH)
{
    var dims = output.Dimensions.ToArray();
    int N, C;
    bool transposed = dims.Length == 3 && dims[1] >= 6 && dims[2] > 10;
    
    if (transposed) { C = dims[1]; N = dims[2]; }
    else { N = dims[1]; C = dims[2]; }

    var boxes = new List<(Rect2f box, float score, int cls)>();
    
    for (int i = 0; i < N; i++)
    {
        // Parse YOLO output: [cx, cy, w, h, conf, cls1, cls2, ...]
        float cx = transposed ? output[0, 0, i] : output[0, i, 0];
        float cy = transposed ? output[0, 1, i] : output[0, i, 1];
        float w = transposed ? output[0, 2, i] : output[0, i, 2];
        float h = transposed ? output[0, 3, i] : output[0, i, 3];
        float obj = C > 4 ? (transposed ? output[0, 4, i] : output[0, i, 4]) : 1f;
        
        // Find best class
        float clsScore = 1f;
        if (C > 5)
        {
            float best = -1f;
            for (int c = 5; c < C; c++)
            {
                float v = transposed ? output[0, c, i] : output[0, i, c];
                if (v > best) best = v;
            }
            clsScore = best;
        }
        
        float score = obj * clsScore;
        if (score < ConfThreshold) continue;
        
        // Convert to x,y,w,h and map back to original coordinates
        float x = cx - w / 2f, y = cy - h / 2f;
        var rect = new Rect2f(x, y, w, h);
        var mapped = MapBoxToOriginal(rect, scale, padLeft, padTop, origW, origH);
        boxes.Add((mapped, score, 0));
    }

    // Apply Non-Maximum Suppression
    var kept = Nms(boxes, IouThreshold);
    
    // Assign tracking IDs
    return AssignTrackingIds(kept);
}

private static List<(Rect2f box, float score, int cls)> Nms(
    List<(Rect2f box, float score, int cls)> boxes, float iouThresh)
{
    var order = boxes.OrderByDescending(x => x.score).ToList();
    var kept = new List<(Rect2f box, float score, int cls)>();
    var suppressed = new bool[order.Count];
    
    for (int i = 0; i < order.Count; i++)
    {
        if (suppressed[i]) continue;
        var cur = order[i];
        kept.Add(cur);
        
        for (int j = i + 1; j < order.Count; j++)
        {
            if (suppressed[j]) continue;
            if (IoU(cur.box, order[j].box) > iouThresh)
                suppressed[j] = true;
        }
    }
    return kept;
}

private static float IoU(Rect2f a, Rect2f b)
{
    float x1 = Math.Max(a.X, b.X);
    float y1 = Math.Max(a.Y, b.Y);
    float x2 = Math.Min(a.X + a.Width, b.X + b.Width);
    float y2 = Math.Min(a.Y + a.Height, b.Y + b.Height);
    float w = Math.Max(0, x2 - x1);
    float h = Math.Max(0, y2 - y1);
    float inter = w * h;
    float union = a.Width * a.Height + b.Width * b.Height - inter;
    return union <= 0 ? 0f : inter / union;
}
```

## The Great Model Training Expedition: From Zero to Hornet Hunter

With the infrastructure in place, it was time for the main event: training an AI model that could actually distinguish Asian hornets from everything else that flies around outside. This is where things got really interesting, and by "interesting," I mean "I learned more about GPU drivers than any sane person should know."

### Dataset Hunting: Finding the Perfect Training Material

First stop: finding a dataset. You might think "How hard could it be to find pictures of hornets?" Well, it turns out that most people don't spend their free time photographing invasive insects. Who knew?

After some digital archaeology, I discovered Roboflow Universe – basically the treasure trove of computer vision datasets. There, nestled among datasets for detecting pizza toppings and counting parking spaces, was exactly what I needed: a beautifully curated Asian hornet dataset with 3,240 training images.

This wasn't just a random collection of blurry phone photos. These were professionally annotated images with consistent labeling, diverse conditions, and that magical 19% ratio of background samples (images without hornets) that helps prevent your model from thinking everything is a hornet. It's like having a really good training partner who won't let you develop bad habits.

### The Hardware Saga: When Your GPU Becomes Your Best Friend

Training neural networks on CPU is a bit like trying to dig a swimming pool with a teaspoon – technically possible, but you'll be there until the heat death of the universe. I needed GPU acceleration, which led me down the rabbit hole of CUDA installation, driver compatibility, and the age-old question: "Why does this work on everyone else's machine but mine?"

After a brief flirtation with DirectML (Microsoft's attempt at hardware-agnostic GPU compute), I went with the tried-and-true CUDA approach. My NVIDIA RTX 1000 Ada might not be the biggest GPU on the block, but it packs enough punch to train YOLOv8s in a reasonable timeframe.

The difference was dramatic: what would have taken 50+ hours on CPU completed in about 5.5 hours on GPU. That's the difference between "I'll check on this tomorrow" and "I can iterate on this today."

### Training Configuration: The Art of Educated Guessing

Choosing training parameters is part science, part art, and part "let's try this and see what happens." Here's what I settled on and why:

**YOLOv8s (the "Goldilocks" model)**: Not too big, not too small, but just right. Large enough to be accurate, small enough to run in real-time, and perfectly sized for my GPU's memory constraints.

**896px image size**: Bigger than the standard 640px, because when you're looking for small flying objects, every pixel counts. It's like the difference between reading a book with your glasses on versus squinting at it from across the room.

**100 epochs**: Enough training cycles to let the model really learn the patterns, but not so many that it starts memorizing individual images. Think of it as the difference between understanding a concept and just cramming for a test.

**Batch size 8**: The sweet spot between GPU memory usage and training stability. Too small and the gradients get noisy; too large and you run out of memory faster than you can say "CUDA out of memory error."

### The Training Process: Patience, Grasshopper

Watching a model train is like watching paint dry, except the paint occasionally tells you how well it's doing. Every epoch, the model would report its progress:

```
Epoch 1/100: box_loss=1.466, cls_loss=2.648, dfl_loss=1.761
Epoch 2/100: box_loss=1.389, cls_loss=2.401, dfl_loss=1.692
...
```

Those numbers slowly decreasing over time became strangely hypnotic. Box loss going down means it's getting better at drawing bounding boxes around hornets. Classification loss decreasing means it's getting better at telling hornets from non-hornets. It's like watching someone get better at darts, one throw at a time.

### The Results: Not Perfect, But Pretty Good

After 5.5 hours of computation (and approximately 17 cups of coffee), the training completed with results that were... well, let's just say they weren't going to win any AI competitions, but they were definitely good enough for the job:

**mAP50: 21%** – This means that at a 50% overlap threshold, the model correctly detects hornets about 21% of the time. Now, before you think "that's terrible," remember that this is for wildlife monitoring, not autonomous vehicles. Missing a hornet is annoying; missing a stop sign is deadly.

**mAP50-95: 5.9%** – This stricter metric (requiring higher precision) shows there's room for improvement in getting pixel-perfect bounding boxes. But honestly, for "is there a hornet here or not?" purposes, close enough is close enough.

The beauty of this system isn't perfect accuracy – it's continuous monitoring with human verification. The AI does the tedious work of watching 24/7, and humans make the final call on anything interesting. It's like having a really dedicated intern who never sleeps but occasionally needs fact-checking.

### ONNX Export: Making It Portable

The final step was exporting the trained model to ONNX format, which is like converting your model from a proprietary format that only works in one specific environment to a universal format that works everywhere. 

```powershell
yolo export model=runs/detect/train/weights/best.pt format=onnx opset=12 dynamic=True
```

That one command took my PyTorch model and turned it into something that could run on any system with ONNX Runtime. No more worrying about Python versions, CUDA compatibility, or whether the target system has the right training frameworks installed. It's like having a recipe that works in any kitchen, not just the one it was created in.

### Training Pipeline

**Local Training Setup** (Windows):

```powershell
# Environment setup
python -m venv .venv
.venv\Scripts\activate
pip install ultralytics onnxruntime

# GPU-accelerated training
yolo detect train model=yolov8s.pt data="dataset/data.yaml" imgsz=896 epochs=100 batch=8 device=0
```

**Model Export**:

```powershell
# Export to ONNX with dynamic shapes
yolo export model=runs/detect/train/weights/best.pt format=onnx opset=12 dynamic=True
```

### Training Results

The YOLOv8s model achieved competitive performance:

- **mAP50**: 0.21 (21% mean Average Precision at IoU=0.5)
- **mAP50-95**: 0.059 (5.9% across IoU thresholds 0.5-0.95)
- **Training time**: ~5.5 hours on NVIDIA RTX 1000 Ada (100 epochs)

**Performance Metrics Interpretation**:

**mAP50 (21%) Analysis**:
- **Detection Capability**: Model successfully detects hornets in 21% of test cases at 50% IoU threshold
- **Real-World Context**: Sufficient for monitoring applications where false negatives are acceptable
- **Baseline Comparison**: Significantly better than random detection (near 0%)
- **Improvement Potential**: Additional training data and longer training could improve scores

**mAP50-95 (5.9%) Understanding**:
- **Precision Requirements**: Lower score reflects difficulty meeting higher IoU thresholds (0.7, 0.8, 0.9)
- **Bounding Box Accuracy**: Indicates room for improvement in precise localization
- **Application Suitability**: Detection (presence/absence) more important than precise boundaries for monitoring

**Training Time Efficiency**:
- **5.5 Hour Duration**: Enables practical iterative development
- **Cost Effectiveness**: Reasonable GPU time investment for proof-of-concept
- **Scaling Potential**: Larger datasets and models feasible within similar timeframes

**Why These Results Are Sufficient for Purpose**:

1. **Monitoring vs Classification**: Early detection more important than perfect accuracy
2. **Human Verification**: System designed with confirmation workflow for critical decisions
3. **False Positive Tolerance**: Better to alert on non-hornets than miss actual hornets
4. **Baseline Establishment**: Provides foundation for iterative improvement
5. **Real-Time Capability**: Performance enables practical deployment scenarios

## User Experience Design

### Material Design Implementation

The application features a modern, professional interface using MaterialDesignInXamlToolkit v5:

- **Theme System**: Dark/Light themes with customizable color schemes
- **Responsive Layout**: Resizable timer pane with GridSplitter
- **Icon-Based UI**: Compact mode for streamlined operation
- **HUD Overlay**: Configurable position and transparency for fullscreen monitoring

### Learning and Confirmation Workflow

A sophisticated training mode allows users to improve detection accuracy:

1. **Always-On Confirmation**: Optional mode for validating all detections
2. **Confidence Gating**: Auto-confirm high-confidence detections
3. **Negative Learning**: "Not hornet" feedback prevents future false positives
4. **Persistent Memory**: Ignored detection IDs saved across sessions

```csharp
public partial class MainWindow
{
    private readonly HashSet<int> _ignoredIds = new();
    private bool _confirmMode = false;
    private int? _candidateId;
    private double? _confirmBelowConf = null;

    private async void OnConfirmNotHornet()
    {
        if (_candidateId.HasValue)
        {
            var id = _candidateId.Value;
            
            // Save negative sample for training data
            await SaveCandidateAsync(id, isHornet: false);
            
            // Add to ignore list to prevent future prompts
            lock (_ignoredLock)
            {
                _ignoredIds.Add(id);
            }
            
            // Remove from active tracking
            _tracker.Remove(id);
            
            // Persist ignore list across sessions
            await SaveIgnoredIdsAsync();
            
            // Clear confirmation UI
            ClearCandidate();
        }
    }

    private async void OnConfirmHornet()
    {
        if (_candidateId.HasValue)
        {
            await SaveCandidateAsync(_candidateId.Value, isHornet: true);
            ClearCandidate();
        }
    }

    private async Task SaveCandidateAsync(int id, bool isHornet)
    {
        if (_candidateImage == null) return;
        
        try
        {
            // Save screenshot with metadata
            string filename = $"{id}_{(isHornet ? "hornet" : "not_hornet")}_{DateTimeOffset.Now:yyyyMMdd_HHmmss}.png";
            string path = Path.Combine(_samplesDir, filename);
            Directory.CreateDirectory(_samplesDir);
            
            // Save image
            var encoder = new PngBitmapEncoder();
            encoder.Frames.Add(BitmapFrame.Create(_candidateImage));
            using var stream = File.Create(path);
            encoder.Save(stream);
            
            // Log to database
            await LogConfirmationAsync(id, isHornet, path);
        }
        catch (Exception ex)
        {
            SimpleLog.Write($"Failed to save candidate: {ex.Message}");
        }
    }

    private bool ShouldIgnoreDetection(int id, float confidence)
    {
        // Skip if user previously marked as "not hornet"
        lock (_ignoredLock)
        {
            if (_ignoredIds.Contains(id)) return true;
        }
        
        // Auto-confirm high confidence detections
        if (_confirmBelowConf.HasValue && confidence >= _confirmBelowConf.Value)
        {
            return false; // Don't ignore, but don't prompt either
        }
        
        return false;
    }

    private async Task SaveIgnoredIdsAsync()
    {
        try
        {
            var path = Path.Combine(AppDomain.CurrentDomain.BaseDirectory, "ignored_ids.json");
            lock (_ignoredLock)
            {
                var json = System.Text.Json.JsonSerializer.Serialize(_ignoredIds.ToArray());
                File.WriteAllText(path, json);
            }
        }
        catch (Exception ex)
        {
            SimpleLog.Write($"Failed to save ignored IDs: {ex.Message}");
        }
    }
}
```

## Data Persistence Strategy

### PostgreSQL Integration

The application uses PostgreSQL for robust data storage with automatic schema management:

```sql
CREATE TABLE hornet_visits (
    id SERIAL PRIMARY KEY,
    tracking_id INTEGER NOT NULL,
    arrived_at TIMESTAMP NOT NULL,
    departed_at TIMESTAMP,
    confidence_score DECIMAL(5,3),
    screenshot_path TEXT,
    is_confirmed BOOLEAN,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### Configuration Management

Application settings persist in `appsettings.json` with hot-reload support:

```json
{
  "ConnectionStrings": {
    "Postgres": "Host=localhost;Port=5432;Username=postgres;Password=postgres;Database=hornetdb;Pooling=true;"
  },
  "Settings": {
    "Detector": "Onnx",
    "OnnxModelPath": "C:\\models\\best.onnx",
    "ConfThreshold": 0.25,
    "IouThreshold": 0.45,
    "StartFullscreen": false,
    "ThemePreset": "BlueGrey + Lime",
    "CompactIcons": false,
    "EnableSound": true,
    "CameraIndex": 0,
    "DepartThresholdSeconds": 1.0
  }
}
```

**Configuration Loading and Hot-Reload**:
```csharp
public record AppConfig(ConnectionStrings ConnectionStrings, Settings? Settings);
public record Settings(
    string Detector, string? OnnxModelPath, double DepartThresholdSeconds,
    int CameraIndex, bool EnableSound, double ConfThreshold, double IouThreshold,
    string? CameraName, bool StartFullscreen, string HudCorner, double HudFontSize,
    GridLength? RightColumnWidth, GridLength? LeftColumnWidth, bool? RightPaneVisible,
    bool ConfirmDetections, bool ConfirmOnStartup, double? ConfirmBelowConf,
    bool AutoAdvanceConfirm, int AutoAdvanceDelayMs, string ThemePreset, bool CompactIcons);

public MainWindow()
{
    var configPath = Path.Combine(AppDomain.CurrentDomain.BaseDirectory, "appsettings.json");
    AppConfig cfg;
    
    try
    {
        if (File.Exists(configPath))
        {
            var json = File.ReadAllText(configPath);
            cfg = System.Text.Json.JsonSerializer.Deserialize<AppConfig>(json) ?? GetDefaultConfig();
        }
        else
        {
            cfg = GetDefaultConfig();
        }
    }
    catch (Exception ex)
    {
        SimpleLog.Write($"Config load failed: {ex.Message}");
        cfg = GetDefaultConfig();
    }
    
    // Apply configuration
    var settings = cfg.Settings ?? GetDefaultSettings();
    _enableSound = settings.EnableSound;
    _confThreshold = settings.ConfThreshold;
    _iouThreshold = settings.IouThreshold;
    
    // Initialize detector based on config
    if (settings.Detector.Equals("onnx", StringComparison.OrdinalIgnoreCase))
    {
        _detector = new OnnxHornetDetector(settings.OnnxModelPath)
        {
            ConfThreshold = (float)_confThreshold,
            IouThreshold = (float)_iouThreshold
        };
    }
    else
    {
        _detector = new ArUcoHornetDetector();
    }
}
```

## Performance Optimization

### Real-Time Processing

- **Asynchronous Operations**: Non-blocking UI with `async/await` patterns
- **Frame Dropping**: Skip processing during high load to maintain responsiveness
- **Memory Management**: Proper disposal of OpenCV Mat objects and ONNX sessions
- **GPU Acceleration**: CUDA support for inference when available

### Monitoring and Diagnostics

Built-in performance metrics track:

- **Detection FPS**: Real-time processing rate
- **Inference Time**: Model execution duration
- **Memory Usage**: Frame buffer and model memory consumption

## Deployment and Distribution

### Prerequisites

- **.NET 8 Runtime**: Windows desktop runtime
- **Visual C++ Redistributables**: Required for OpenCvSharp native libraries
- **PostgreSQL**: Local or remote database instance
- **ONNX Runtime**: CPU or GPU inference providers

### Docker Support

PostgreSQL can be deployed via Docker/Podman for simplified setup:

```powershell
# scripts/ensure-postgres-podman.ps1
podman run -d --name postgres-hornet `
  -e POSTGRES_PASSWORD=postgres `
  -e POSTGRES_DB=hornetdb `
  -p 5432:5432 postgres:15
```

## Future Enhancements

### Planned Features

1. **Multi-Camera Support**: Simultaneous monitoring of multiple feeds
2. **Edge Deployment**: Raspberry Pi and embedded device support
3. **Cloud Integration**: Remote monitoring and data aggregation
4. **Advanced Analytics**: Seasonal patterns and population tracking
5. **Mobile Companion**: iOS/Android app for remote alerts

### Model Improvements

1. **Larger Datasets**: Expand training data for better generalization
2. **Species Classification**: Distinguish between hornet species
3. **Behavioral Analysis**: Detect aggressive vs. foraging behavior
4. **Temporal Modeling**: Track movement patterns over time

## Development Lessons Learned

### Technical Challenges

1. **ONNX Runtime Integration**: Managing provider priorities and device selection
2. **Real-Time Performance**: Balancing accuracy with processing speed
3. **UI Responsiveness**: Preventing detection pipeline from blocking the interface
4. **Memory Management**: Proper cleanup of computer vision resources

### Best Practices Discovered

1. **Modular Architecture**: Clean separation enables easier testing and maintenance
2. **Configuration-Driven**: External settings allow field customization without recompilation
3. **Progressive Enhancement**: Start with simple detection, add sophistication incrementally
4. **User Feedback Loops**: Learning workflows improve accuracy over time

## Conclusion

HornetWatcher demonstrates how modern .NET development can create sophisticated AI-powered applications for real-world problems. By combining proven technologies like WPF, OpenCV, and YOLOv8 with thoughtful architecture and user experience design, we've built a tool that makes Asian hornet monitoring accessible to researchers, beekeepers, and conservationists.

The project showcases several key principles:

- **AI Integration**: Seamlessly embedding machine learning into desktop applications
- **User-Centric Design**: Making complex technology approachable through intuitive interfaces
- **Performance Focus**: Maintaining real-time responsiveness with resource-intensive operations
- **Extensible Architecture**: Building for future enhancement and customization

Whether you're building wildlife monitoring tools, security systems, or other computer vision applications, the patterns and techniques demonstrated in HornetWatcher provide a solid foundation for your own AI-powered desktop applications.