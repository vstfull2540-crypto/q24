# LA-2A Arm Nong Muang — FIXED32 Rotary DrawLine Compile Fix

This project uses JUCE 8 and builds a VST3 plug-in for Windows via CMake/GitHub Actions. FIXED32 resolves the MSVC C2661 errors in the rotary indicator by passing `juce::Line<float>` objects to the JUCE `Graphics::drawLine` overload.

## Rotary controls
- GAIN and PEAK REDUCTION are real `juce::Slider` controls using `RotaryHorizontalVerticalDrag`.
- The custom `PointerLookAndFeel` paints a black bezel, metallic silver face, inner bevel, and value-driven indicator.
- SliderAttachment binds each rotary to APVTS parameters `gain` and `peak`, so automation and saved host state use the actual parameter values.
- The numeric labels below the knobs update from the current slider values, are positioned lower, and have transparent backgrounds (no black box/border).
- The processor applies gain and peak-reduction values in `processBlock()`.

## Build
Open this repository in GitHub and run **Actions → Build LA-2A VST3 (Windows)**. A successful archive/ZIP check is not a substitute for a Windows compiler build; verify the Actions result before installing in Nuendo 15.

## Install
Use the `LA2A-Windows-VST3` artifact produced by a successful workflow run and install/copy the VST3 bundle according to your Windows VST3 setup.


## Model loading (.nam / .gen)

Click the model-load area to select a `.nam` or `.gen` file. Standard `.nam` files use NeuralAmpModelerCore's file loader. `.gen` files are parsed as JSON and passed to the Core JSON model loader, so `.gen` is supported when its contents are a valid JSON model configuration compatible with the installed NeuralAmpModelerCore version (the extension alone cannot make a proprietary/incompatible `.gen` format loadable). On success, the UI shows `Loaded: <filename>` beneath the model-load area and the filename is saved with the plugin state. When the project is reopened, the plugin attempts to reload the model from its saved full path. Keep the model file at that path for automatic restoration. Loading requires a mono-in/mono-out model; separate model instances are created for left and right channels.


## Model file compatibility (FIXED44)

- `.nam`: loaded with NeuralAmpModelerCore. The included example `.nam` is a NAM 0.7.0 `SlimmableContainer` model; the project pins NeuralAmpModelerCore v0.5.4.
- `.gen`: the extension is not a universal model format. This build accepts `.gen` only when its contents are a valid NAM JSON model. Binary/proprietary `.gen` files require the format's own decoder/SDK and are rejected with a clear error instead of being reported as loaded.
- A successful load displays `Loaded: <filename>` in white. Failed loads display an error in red.
- This plugin currently requires mono-in / mono-out model DSPs.

The supplied `Shadow Hills ... .gen` sample is binary (not a NAM JSON document), so it cannot be truthfully supported by NeuralAmpModelerCore alone. Do not rename it to `.nam`; that does not convert its contents.
