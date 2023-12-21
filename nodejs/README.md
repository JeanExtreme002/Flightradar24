# FlightRadarAPI
Unofficial SDK for [FlightRadar24](https://www.flightradar24.com/) written in JavaScript.

If you want to use the data collected using this SDK commercially, you need to subscribe to the [Business plan](https://www.flightradar24.com/premium/).</br>
See more information at: https://www.flightradar24.com/terms-and-conditions

[![Python Package](https://github.com/JeanExtreme002/FlightRadarAPI/workflows/Python%20Package/badge.svg)](https://github.com/JeanExtreme002/FlightRadarAPI/actions)
[![Pypi](https://img.shields.io/pypi/v/FlightRadarAPI)](https://pypi.org/project/FlightRadarAPI/)
[![License](https://img.shields.io/pypi/l/FlightRadarAPI)](https://pypi.org/project/FlightRadarAPI/)
[![Python Version](https://img.shields.io/badge/python-3.7%20%7C%203.8%20%7C%203.9%20%7C%203.10%20%7C%203.11-blue)](https://pypi.org/project/FlightRadarAPI/)
[![Downloads](https://static.pepy.tech/personalized-badge/flightradarapi?period=total&units=international_system&left_color=grey&right_color=orange&left_text=Downloads)](https://pypi.org/project/FlightRadarAPI/)

## Installing FlightRadarAPI:
```
npm install flightradarapi
```

## Basic Usage:
Import the class `FlightRadar24API` and create an instance of it.
```javascript
const {FlightRadar24API} = require("flightradarapi");
const fr_api = new FlightRadar24API();
```

**Getting flights list:**
```javascript
let flights = await fr_api.get_flights(...);  // Returns a list of Flight objects
```

**Getting airports list:**
```javascript
let airports = await fr_api.get_airports(...);  // Returns a list of Airport objects
```

**Getting airlines list:**
```javascript
let airlines = await fr_api.get_airlines();
```

**Getting zones list:**
```javascript
let zones = await fr_api.get_zones();
```

### Getting flight and airport details
You can also get more information about a specific flight such as: estimated time, trail, aircraft details, etc.
```javascript
let flight_details = await fr_api.get_flight_details(flight);
flight.set_flight_details(flight_details);

console.log("Flying to", flight.destination_airport_name);
```

Or get more information about a specific airport such as: runways, temperature, arrived flights, etc.
```javascript
let airport_details = await fr_api.get_airport_details(airport.icao);
```
Arrivals and departures can have a limit `flight_limit` (max value is 100) to display. When you need to reach more than 100 flights you can use additional parameter `page` to view other pages.


## Get flights above your position:
The `get_bounds_by_point(...)` method has parameters `latitude` and `longitude` for your position and `radius` for the distance in meters from your position to designate a tracking area.
```javascript
// Your point is 52°34'04.7"N 13°16'57.5"E from Google Maps and radius 2km
let bounds = fr_api.get_bounds_by_point(52.567967, 13.282644, 2000);

let flights = fr_api.get_flights(null, bounds)
```

## Filtering flights and airports:
The `get_flights(...)` method has some parameters to search for flights by: area line, bounds (customized coordinates 
or obtained by the `get_zones()` method), aircraft registration or aircraft type. See the example below:
```javascript
let airline_icao = "UAE";
let aircraft_type = "B77W";

# You may also set a custom region, such as: bounds = "73,-12,-156,38"
let zone = await fr_api.get_zones()["northamerica"];
let bounds = fr_api.get_bounds(zone);

let emirates_flights = await fr_api.get_flights(airline_icao, bounds, null, aircraft_type);
```
There are more configurations that you may set by using the `set_flight_tracker_config(...)` method. See the method documentation
for more information.

**Getting airport by ICAO or IATA:**
```javascript
let lukla_airport = await fr_api.get_airport("VNLK");
```

## Getting the distance between flights and airports:
The `Flight` and `Airport` classes inherit from `Entity`, which contains the `get_distance_from(...)` method. That method
returns the distance between the self instance and another entity in kilometers. Example:
```javascript
let airport = await fr_api.get_airport("KJFK")
let distance = flight.get_distance_from(airport);

console.log("The flight is", distance, "km away from the airport.");
```

## Setting and getting Real-time Flight Tracker parameters:
Set it by using the `set_flight_tracker_config(...)` method. It receives a `FlightTrackerConfig` dataclass instance, but
you can also use keyword arguments directly to the method.

Get the current configuration with the `get_flight_tracker_config()` method, that returns a `FlightTrackerConfig` 
instance. Note: creating a new `FlightTrackerConfig` instance means resetting all parameters to default.
```javascript
let flight_tracker = fr_api.get_flight_tracker_config();
let flight_tracker.limit = 10

fr_api.set_flight_tracker_config(flight_tracker, ...);

let flights = fr_api.get_flights(...);  // Returns only 10 flights
```