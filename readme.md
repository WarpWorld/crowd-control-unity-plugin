# Crowd Control Unity Plugin

This plugin integrates [Crowd Control](https://crowdcontrol.live/) functionality into Unity Projects.
 - Easy drag and drop prefab for quick game integration.
 - Documentation
 - Compatible with  .NET Framework 3.5, .NET Framework 4.5 and .NET Standard 2.0. 
 - Working example included inside Assets -> WarpWorld -> DemoGame
 
## Integration Steps
 - Get the appropriate package for your project. This information can be found under File -> Build Settings -> Player Settings -> Scripting Runtime Version. If "Scripting Runtime Version" field isn't present, that means you should use the .NET Standard 2.0. pack. 
 - After selecting the appropriate unity package, import it into your project.
 - Drag "Crowd Control Manager" into Scene, which is located under Assets -> WarpWorld -> Prefabs. Set appropriate GameID after speaking with Warp World Staff. Automatic application process coming soon.
 - Add custom effects that either inherits from CCEffect, CCEffectParameters, CCEffectBidWar or CCEffectTimed. Usage is outlined in documentation. 
 - Customize UI underneath Crowd Control Overlay to fit your game's theme.
 - Inside the Crowd Control Manager, add the list of IDs into CC Effect Entries linked to each effect (every entry must be unique).
 - Submit Game and Effect List for approval (coming soon, for now contract Warp World Staff)

## Version Requirements
 * .NET Framework 3.5; Unity 2018.2.1f1 or greater
 * .NET Framework 4.5: Unity 2018.2.1f1 or greater
 * .NET Standard 2.0: Unity 2019.2.0f1 or greater
 
## Documentation

For our full documentation, examples and tutorials, head to https://developer.crowdcontrol.live/unity/
