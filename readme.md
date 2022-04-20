# Crowd Control Unity Plugin

This plugin integrates [Crowd Control](https://crowdcontrol.live/) functionality into Unity Projects.
 - Easy drag and drop prefab for quick game integration.
 - [Documentation](https://wiki.warp.world/en/CrowdControl/Guides/Unity)
 - Compatible with  .NET Framework 3.5, .NET Framework 4.5 and .NET Standard 2.0. 
 - Working example included inside Assets -> WarpWorld -> DemoGame
 
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
Name          |Type          |Description
------------- | -------------|-----------------------------------------------------------------------------
PauseEffect(**CCEffectTimed** effect)|void|Resumes a timed effect
ResumeEffect(**CCEffectTimed** effect)|void|Pauses a timed effect
ResetEffect(**CCEffectTimed** effect)|void|Resets a timed effect to its original start length

#### Public Attributes
Name          |Type          |Description
------------- | -------------|-----------------------------------------------------------------------------
delayBetweenEffects|float|Time to wait after triggering an effect before attempting to trigger another
isAuthenticated|bool|Whether the connection to the server is currently initializing
isConnecting|bool|If the client is in the process of connecting to the server
isConnected|bool|Are you connected to the server?
disconnectedFromError|bool|The latest disconnect occured due to an error

#### Static Properties
Name          |Type          |Description
------------- | -------------|-----------------------------------------------------------------------------
instance|**CrowdControl**|Singleton instance to the manager. Will be <see langword="null"/> if the behaviour isn't in the scene
testUser|**TwitchUser**|Reference to the test user object. Used to dispatch local effects
crowdUser|**TwitchUser**|Reference to the crowd user object. Used to dispatch pooled effects
anonymousUser|**TwitchUser**|Reference to the crowd user object. Used to dispatch effects with an unknown contributor
streamerUser|**TwitchUser**|Reference to the streamer user object.

#### Events
Name          |Type          |Description
------------- | -------------|-----------------------------------------------------------------------------
OnConnecting|Action|Invoked when attempting a connection to the Crowd Control server
OnConnectionError|Action|Invoked when the connection to the Crowd Control server has failed
OnAuthenticated|Action|Invoked after having being authenticated by the Crowd Control server
OnConnected|Action|Invoked when successfully connected to the Crowd Control server
OnDisconnected|Action|Invoked when disconnected from the Crowd Control server
OnNoToken|Action|Invoked when trying to start a session without a saved tokne
OnSubmitTempToken|Action|Invoked when submitting a temporary token to the Crowd Control server
OnTempTokenFailure|Action|Invoked when the server rejects the user's temporary token
OnEffectTrigger|Action<**CCEffectInstance**>|Invoked when an effect is successfully triggered
OnEffectStart|Action<**CCEffectInstanceTimed**>|Invoked when a timed effect starts
OnEffectStop|Action<**CCEffectInstanceTimed**>|Invoked when a timed effect stops
OnEffectPause|Action<**CCEffectInstanceTimed**>|Invoked when a timed effect pauses
OnEffectResume|Action<**CCEffectInstanceTimed**>|Invoked when a timed effect resumes
OnEffectReset|Action<**CCEffectInstanceTimed**>|Invoked when a timed effect gets reset

### TwitchUser
Holds information about Twitch users interacting with the Crowd Control session.

#### Public Attributes
Name          |Type          |Description
------------- | -------------|-----------------------------------------------------------------------------
id|**ulong**|Unique Twitch user identifier
name|**string**|Unique Twitch user name. Always lowercase.
displayName|**string**|User's Display Name
profileIconUrl|**string**|URL to download the profile icon from
profileIcon|**Texture2D**|Profile icon downloaded into a 2D texture. Can be null 
profileIconColor|**Color**|Tint of the user's icon

### CCEffectBase
An effect that can be triggered a Crowd Control session.

#### Properties
Name          |Type          |Description
------------- | -------------|-----------------------------------------------------------------------------
displayName|**string**|Name of the effect displayed to the users
description|**string**|Information about the effect, displayed in the extension
price|**uint**|How much the effect costs to trigger
icon|**Sprite**|Image to display in the CrowdControl Twitch extension and in the onscreen overlay
iconColor|**Color**|Tint of the icon
maxRetries|**int**|Number of retries before the effect instance fails
retryDelay|**float**|Delay in seconds before retrying to trigger an effect instance
pendingDelay|**float**|Delay in seconds to wait before triggering the next effect instance

#### Public Methods
Name          |Type          |Description
------------- | -------------|-----------------------------------------------------------------------------
ToggleSellable(**bool** sellable)|**void**|Toggles whether this effect can currently be sold during this session
ToggleVisible(**bool** visible)|**void**|Toggles whether this effect is visible in the menu during this session
*virtual* Params()|**string**|Returns a list of parameters for this effect as a string. Used by **CCEffectParameters** and **CCEffectBidWar** but can be overriden by custom effect classes.
*virtual* CanBeRan()|**bool**|overriden by base classes that return true if the effect is able to be ran at this time
*virtual* RegisterParameters(**CCEffectEntries** effectEntries)|**void**|Takes the list of this effect's parameters and adds them to the effect list. Used by **CCEffectParameters** and **CCEffectBidWar** but can be overriden by custom effect classes.
*virtual* HasParameterID(**uint** id)|**bool**|Returns true if this bid war is the parent of the parameter ID. Used by **CCEffectParameters** and **CCEffectBidWar** but can be overriden by custom effect classes.

### CCEffect : Inherits from CCEffectBase
A single-trigger effect with no extra properties



### CCEffectParameters : Inherits from CCEffectBase
A Crowd Control effect that handles parameters

#### Properties
Name          |Type          |Description
------------- | -------------|-----------------------------------------------------------------------------
ParameterEntries|**Dictionary<uint, ParameterEntry>**|List of parameter choices for this effect

#### Public Methods
Name          |Type          |Description
------------- | -------------|-----------------------------------------------------------------------------
AddParameters(params **object[] prms**)|**void**|Dynamically adds object(s) to the parameter list
ClearParameters()|**void**|Clears the established parameter list
*override* Params()|**string**|Returns a list of parameters for this effect as a string. 
*override* RegisterParameters(**CCEffectEntries** effectEntries)|**void**|Takes the list of this effect's parameters and adds them to the effect list. 
*override* HasParameterID(**uint** id)|**bool**|Returns true if this bid war is the parent of the parameter ID.


### CCEffectTimed : Inherits from CCEffectBase
A Crowd Control effect active for a given duration of time

#### Properties
Name          |Type          |Description
------------- | -------------|-----------------------------------------------------------------------------
duration|**float**|Duration in seconds before the effect automatically ends
paused|**bool**|Is the timer paused?
displayType|**DisplayType**|Whether the timer displays text or a fill graphic

#### Protected Internal Methods
Name          |Type          |Description
------------- | -------------|-----------------------------------------------------------------------------
|*abstract* OnPauseEffect()|**void**|Invoked when an effect instance is paused
|*abstract* OnResumeEffect()|**void**|Invoked when an effect instance is resumed
|*abstract* OnResetEffect()|**void**|Invoked when an effect instance is reset
|*abstract* OnStopEffect(**CCEffectInstanceTimed** effectInstance, **bool** force)|**bool**|Attempts to stop an effect. Force is set to true if the effect doesn't end when time runs out. Returns true if stopping the effect was successful.

### CCEffectBidWar : Inherits from CCEffectBase
Base effect for bid war effects

#### Properties
Name          |Type          |Description
------------- | -------------|-----------------------------------------------------------------------------
||

#### Public Methods
Name          |Type          |Description
------------- | -------------|-----------------------------------------------------------------------------
||

