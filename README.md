# node-red-embedded
Node red nodes used by the [signalk-node-red](https://github.com/SignalK/signalk-node-red) plugin which embeds node-red into [signalk-node-server](https://github.com/SignalK/signalk-server-node).

Please note that these nodes only work when node-red is embedded in signalk-node-server.

See the examples folder for flows from the following examples..

# Examples

## Send an adjusted depth value
![Alt text](https://raw.githubusercontent.com/SignalK/node-red-embedded/master/screens/adjust-depth.jpeg)

## Switch relays based on complex conditions (example turns on/off the anchor light and ACR automatically)
![Alt text](https://raw.githubusercontent.com/SignalK/node-red-embedded/master/screens/switch-automation.jpeg)

## Send an alarm when wind speed is high
![Alt text](https://raw.githubusercontent.com/SignalK/node-red-embedded/master/screens/high-wind-speed-alarm.jpeg)

## Send an alarm when the starter battery voltage is low for more and 60 seconds. (this keeps the alarm from going off while starting the engine)
![Alt text](https://raw.githubusercontent.com/SignalK/node-red-embedded/master/screens/starter-voltage-alarm.jpeg)


## Send an alarm when the AC input voltage is low and the boat is in its slip
![Alt text](https://raw.githubusercontent.com/SignalK/node-red-embedded/master/screens/ac-in-voltage-alarm.jpeg)

## Send a tweet when leaving or arriving at the slip
![Alt text](https://raw.githubusercontent.com/SignalK/node-red-embedded/master/screens/tweet-when-leaving.jpeg)

## GPIO and put handler 
Sends the state of a digital gpio pin into Signal K and allows at PUT request to the server to set the state of a pin.
![Alt text](https://raw.githubusercontent.com/SignalK/node-red-embedded/master/screens/gpio.jpeg)

# Available Nodes

## signalk-on-delta

Input that sends messages for every delta the server receives. Optionally flatten the deltas (see signalk-flatten-delta below)

## signalk-subscribe

Input that sends messages for every delta from the configured path. Optionally flatten the deltas (see signalk-flatten-delta below)

## signalk-flatten-delta

Function that flatten deltas from signalk-on-delta.

The output payload will be the value and the topic will be the path, The payload will also include context, $source, and source.

```
{
  "topic": "navigation.speedOverGround",
  "payload": 2.45,
  "source": {"label":"actisense","type":"NMEA2000","pgn":129026,"src":"3"},
  "context": "vessels.self",
  "$source": "actisense.3"
}
```

## signalk-input-handler

Input that allows a flow to modify or filter deltas before they get to the server. Once the data is modified, it should be connected to the signalk-input-handler-next node.

## signalk-input-handler-next

Output that should be used to send a delta on after processing using the signalk-input-handler node.

## signalk-geofence-switch

Function that checks if a vessel is inside of the specified geofence

## signalk-delay

Function that will delay sending the input until the value has been the same for the given timout

For example, you could use this to send a low starter battery voltage alarm only if it stays low for 5 seconds.

## signalk-send-pathvalue

Output that sends a delta through the server. Input should be path and value

```
{
  "path":"navigation.speedOverGround",
  "value":20.45
}
```

## signalk-notification

Input that sends a message when a notification is received. Configuration allows messages for a specific notification or any notification. The notification state can also be specified. Payload will be the notification path and value.

```
{
  "path: "notifications.anchorAlarm",
  "value" : {
    "state" : "emergency",
    "method" : [
      "visual",
      "sound"
    ],
    "timestamp" : "2018-06-15T15:01:55.007Z",
    "message" : "Anchor Alarm - Emergency"
  }
}
```

## signalk-geofence

Input that checks if a vessel is inside of the specified geofence

## signalk-put-handler

Input that sends messages when a PUT is sent to the server for the given path

## signalk-send-delta

Output that sends a delta to the server. Input should be a fully formed SignalK delta

## signalk-send-put

Output that send a SignalK put request via `app.putSelfPath`. Input should be the value to put.

## signalk-send-notification

Output that sends a SignalK notification

If the input payload is an object, then it will use the keys path, state, method, and message. Example below. Otherwise  it will use the configured values.

To specify all the info, send:
```
{payload: {
  "path":"notifications.testNotification",
  "state":"alarm",
  "method":["visual","sound"],
  "message":"this is a notification message"
}}
```

Or to specify some of the info, send:
```
{payload: {
  "state":"normal",
}}
```

## signalk-send-nmea2000

Output that sends out NMEA 2000 messages. Input payload can be canboat json format or a raw Actisense formatted string

## signalk-send-nmea0183

Output that sends out NMEA 0183 messages. Input payload can be an 0183 formatted string.
