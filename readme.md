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
DisableEffect(**CCEffectTimed** effect)|void|Pauses a timed effect
EnableEffect(**CCEffectTimed** effect)|void|Resumes a timed effect
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
*override* RegisterParameters(**CCEffectEntries** effectEntries)|**void**|Takes the list of this effect's parameters and adds them to the effect list. 
*override* HasParameterID(**uint** id)|**bool**|Returns true if this bid war is the parent of the parameter ID.

### ParameterEntry : Inherits from BidWarEntry
A parameter type that can be added to a class that inherits from CCEffectParameters

#### Properties
Name          |Type          |Description
------------- | -------------|-----------------------------------------------------------------------------
ParamKind|**Kind**|What kind of parameter it is. Can either be Kind.Item or Kind.Quantity
Min|**uint**|For quantity effects. The minimum quantity the viewer can purchase for this effect.
Max|**uint**|For quantity effects. The maximum quantity the viewer can purchase for this effect.
Options|ParameterOption[]||For item effects. A list of parameters that can be used for this effect.

### ParameterOption 
An item entry for a list of item effects

#### Properties
Name          |Type          |Description
------------- | -------------|-----------------------------------------------------------------------------
Name|**string**|Name of the item

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
BidWarEntries|**Dictionary<uint, BidWarEntry>**|A collection of every bid war entry registered for this effect
*override* Name|**string**|Gets the name of the current bid war winner. If no winner or if the winner has no name, it will return the name of the bid war effect
*override* Icon|**Sprite**|Gets the icon of the current bid war winner. If no winner or if the winner has no icon, it will return the icon of the bid war effect
*override* IconColor|**Color**|Gets the tint of the current bid war winner. If no winner or if the winner has no icon tint, it will return the tint of the bid war effect

### BidWarEntry 
An entry that is assigned to a class that inherits from **CCEffectBidWar**

#### Properties
Name          |Type          |Description
------------- | -------------|-----------------------------------------------------------------------------
Name|**string**|The name of the bid war option.
Sprite|**Sprite**|The sprite of the bid war option.
Tint|**Color**|The tint of the bid war option.

#### Public Methods
Name          |Type          |Description
------------- | -------------|-----------------------------------------------------------------------------
RegisterBidWarEntry(**BidWarEntry** entry, **CCEffectEntries** effectEntries)|**void**|Adds a new paramter to the bid war list
PlaceBid(**uint** bidID, **uint** amount)|bool|Places a bid for one of the parameters. Returns true if there's a new winner with that bid
*override* RegisterParameters(**CCEffectEntries** effectEntries)|**void**|Takes the list of this effect's parameters and adds them to the effect list
*override* HasParameterID(**uint** id)|**bool**|Returns true if this bid war is the parent of the parameter ID

### CCEffectInstance
An instance of a running Crowd Control effect. Disposed once the effect is complete.

#### Properties
Name          |Type          |Description
------------- | -------------|-----------------------------------------------------------------------------
effect|**CCEffectBase**|The effect this isntance was created from
user|**TwitchUser**|The Twitch user who triggered the effect.
retryCount|**int**|How many times this effect was attempted to be ran
unscaledStartTime|**float**|Unscaled game time when the effect was triggered
isTest|**bool**|True if this effect was triggered by a local test

### CCEffectInstanceParameters : Inherits from CCEffectInstance
An instance of a Crowd Control effect with parameters.

#### Public Methods
Name          |Type          |Description
------------- | -------------|-----------------------------------------------------------------------------
AssignParameters(**string** newParams)|**void**|Takes a list of parameters separated by a comma and assigns them to this effect instance
GetParameter(**int** index)|**string**|Gets the parameter entry at a specific index

### CCEffectInstanceTimed : Inherits from CCEffectInstance
An instance of a running Crowd Control effect. Disposed once the effect is complete.

#### Properties
Name          |Type          |Description
------------- | -------------|-----------------------------------------------------------------------------
unscaledEndTime|**float**|Unscaled game time when the timed effect will end. Updated on resume
unscaledTimeLeft|**float**|Unscaled game time left to execute
isActive|**bool**|Whether the effect instance is active. True when the related Effect behaviour is enabled
isPaused|**bool**|Whether the effect instance is paused or not
shouldBeRunning|**bool**|Checks if the effect should be running or not, then applies the paused state based on it

### CCEffectInstanceBidWar : Inherits from CCEffectInstance
An instance of a running Crowd Control Bid War effect.
#### Properties
Name          |Type          |Description
------------- | -------------|-----------------------------------------------------------------------------
BidName|**string**|The name of what was bid on
BidAmount|**uint**|The amount of coins spent on this bid
