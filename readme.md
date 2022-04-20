# Crowd Control Unity Plugin

This plugin integrates [Crowd Control](https://crowdcontrol.live/) functionality into Unity Projects.
 - Easy drag and drop prefab for quick game integration.
 - [Documentation](https://wiki.warp.world/en/CrowdControl/Guides/Unity)
 - Compatible with .NET Framework 4.5 and .NET Standard 2.0. NET Framework 3.5 support coming soon.
 - Working example inside Assets -> WarpWorld -> DemoGame
 
## Integration Steps
 - Drag "Crowd Control Prefab" into Scene. Set appropriate GameID after speaking with Warp World Staff. Automatic application process coming soon.
 - Add custom effects that either inherits from CCEffect, CCEffectParameters, CCEffectBidWar or CCEffectTimed. Usage is outlined in documentation. 
 - Customize UI underneath Crowd Control Overlay to fit your game's theme.
 - Inside the Crowd Control Manager, add the list of IDs into CC Effect Entries linked to each effect (every entry must be unique).
 - Submit Game and Effect List for approval (coming soon, for now contract Warp World Staff)

## Documentation

### Crowd Control Manager (WarpWorld.CrowdControl)

This class instance is responsible for sending, receiving and processing all the effects. 

#### Public Member Functions
Name          |Type          |Description
------------- | -------------|-----------------------------------------------------------------------------
Connect()     | void         |Connects the client to the server and attempts to start a Crowd Control Session
Disconnect()  | void         |Disconnects the clinet from the Crowd Control server
RegisterEffect()|void        |Registers an effect with the client's dabatase
EffectIsRegistered()|bool    |Returns true if the effect has been registered by the client
ToggleEffectSellable(**uint** effectID, **bool** sellable)|void|Toggles whether an effect can currently be sold during this session
ToggleEffectSellable(**uint** effectID, **bool** visible)|void|Toggles whether an effect is visible in the menu during this session
SubmitTempToken(**string** token)|void|Submits Temporary Token to the server. Gets a response whether or not it was valid and begins the Crowd Control Session if successful
TestEffect(**CCEffectBase** effect)|void|Takes an effect and triggers it locally. For testing purposes only
StopAllEffects()|void|Forcefully terminates all pending and running effects
CancelEffect(**CCEffectInstance** effectInstance)|void|Cancels an effect that's sitting in the queue. Sender will receive a refund
HasRunningEffects()|bool|Returns true if at least one timed effect is currently running
IsRunning(**CCEffectTimed** effect)|bool|Returns true if at least one timed effect is currently running
IsPaused(**CCEffectTimed** effect)|bool|Returns true if timed effect is paused

#### Static Public Member Functions
PauseEffect(**CCEffectTimed** effect)|void|Resumes a timed effect
ResumeEffect(**CCEffectTimed** effect)|void|Pauses a timed effect
ResetEffect(**CCEffectTimed** effect)|void|Resets a timed effect to its original start time
