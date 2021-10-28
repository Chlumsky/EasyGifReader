
# EasyGifReader

EasyGifReader is a single-class C++ library that aims to simplify reading an animated GIF file. It is built on top of and **depends on giflib**. While giflib does decode the binary format of the GIF file and converts it to structured data, there is still a lot left to do to make sense of this data before the animation can be presented, such as:

- Dealing with **indexed color** bitmaps and color maps.
- Compositing **partial frames** - ones that do not cover the entire area of the GIF.
- Correctly handling frame **disposal modes** - an optimization that allows each frame to use prior frames as its basis in multiple ways.
- Resolving **transparency**.
- Parsing **loop count** information from extension blocks.

Some of the data in a GIF file is ambiguous or interpreted inconsistently across software. In these cases, the library replicates the behavior of Google **Chrome**, which can be considered as the industry standard due to its status as the largest web browser. If you find a GIF file that behaves differently in Chrome, please report it as a bug.

## Installation

To use this library in your project, first make sure that **giflib** is available in the include path and is being linked. Then, simply add the files `EasyGifReader.h` and `EasyGifReader.cpp` to your C++ project.

## Usage

Using the `EasyGifReader` class is very straightforward and intuitive. The following example demonstrates how a GIF file can be loaded, and each of its frames inspected.

```c++
try {
    EasyGifReader gifReader = EasyGifReader::openFile("animation.gif");
    int frameCount = gifReader.frameCount();
    int width = gifReader.width();
    int height = gifReader.height();
    bool loop = gifReader.repeatInfinitely();
    for (const EasyGifReader::Frame &frame : gifReader) {
        const std::uint32_t *framePixels = frame.pixels();
        double frameDuration = frame.duration().seconds();
        // TODO display framePixels for frameDuration seconds
    }
} catch (EasyGifReader::Error gifError) {
    logError("Reading GIF file failed");
}
```

To clarify, `framePixels` is an array of `width*height` 32-bit pixels in the RGBA byte order. Due to limitations of the GIF format, the alpha component will always be either `0xFF` or `0x00`.
