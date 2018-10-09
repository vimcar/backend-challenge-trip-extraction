# Vimcar Coding Challenge: Trip Extraction

One of Vimcar's successful products is the digital logbook. The purpose
of a logbook is to maintain a complete, consistent list of all trips a 
vehicle has made over time. There are many different use-cases for this, one 
of them is taxation. Our customers e.g. maintain a logbook, to keep track of
how many trips were made privately and how many kilometers were driven in a
business context.

An essential part in the creation of a fully tax law compliant and user-loved 
digital logbook is the translation of raw vehicle information (like current 
position, speed, mileage, etc.) into trips, which the vehicle actually made on 
the road. A trip is basically the combination of the location and time of the 
start as well as the location and time of the end of a journey.

##### At Vimcar we call this process: Trip Extraction

The process is about identifying movement, breaks and stops in the smartest 
possible way, to be able to create exactly the trips the user expects to see
in his logbook. 

As the simplest example, when driving from A to B, a user will probably have 
interruptions like stopping at red lights or traffic jams. The expected 
behaviour in a logbook is to have one trip accounting for the whole journey, 
and not broken trips like from A to first red light etc.

## The Task
The goal of the coding challenge is to write a program, which creates a JSON 
file that contains a list of Trips. The schema of a trip is defined below, 
see "The Trip Entity". 

These Trips need to be extracted out of raw vehicle data, a GPS stream in this
case. The schema of the vehicle data is defined below, see "The Vehicle Data".  

An example of the expected result can be found in `data/trips.json`. This is 
explicitly only an example of the resulting file format, this is not the 
expected list of trips to be extracted out of the given raw vehicle data – 
especially the distances, timestamps and coordinates vary. 

### The Vehicle Data
In this coding challenge our raw vehicle data will be **a stream of GPS 
coordinates**. In our actual products we process additional data from other 
sources, for example from the vehicle's on-board computer, to optimize the 
accuracy of our trip extraction process even more.

The GPS stream is included in this repository as a JSON file inside the data
folder. It is called: `data/waypoints.json` and it contains a list of 
"Waypoints". A Waypoint includes the following data:
- `timestamp` – string, a timestamp in ISO 8601 format
- `lat` – float, Latitude of the GPS coordinate (WGS84)
- `lng` – float, Longitude of the GPS coordinate (WGS84)

#### Example:
```json
  {
    "timestamp": "2018-08-10T20:04:22Z",
    "lat": 51.54987,
    "lng": 12.41039
  }
```

### The Trip Entity
In the coding challenge, a trip only contains the start, end and distance. 
No other information like GPS traces or addresses are needed.

- `start` – object, the start of the trip in Waypoint schema (see above)
- `end` – object, the end of the trip in Waypoint schema (see above)
- `distance` – int, the distance of the trip in meters


#### Example:
```json
  {
    "start": {
      "timestamp": "2018-08-12T10:01:38Z",
      "lat": 51.54987,
      "lng": 12.41039
    },
    "end": {
      "timestamp": "2018-08-12T10:31:13Z",
      "lat": 51.60519,
      "lng": 12.3008
    },
    "distance": 18421
  }
```

### The Trip Distance
The distance of the overall trip needs to be defined in meters and needs to be 
calculated by the sum of all distances between the individual waypoints of the
trip. There are several algorithms with different precision to calculate the 
distance between two GPS coordinates. It is totally fine, to use a 3rd party
library for this calculation. The minimum requirement for the coding 
challenge is, that the curvature of the earth is considered.

### Trip Definition
When extracting trips out of a list of waypoints, certain rules need to be 
applied in order to decide whether a new trip needs to start, the current trip 
needs to continue or should end or if a waypoint should be considered as noise
and needs to be ignored.

Please consider the following rules, when implementing the trip extraction:
- A vehicle may occasionally sends GPS updates, even if it not moving
- The precision of GPS positioning is not accurate on meter-level. Distances 
between waypoints < 15 meters should be ignored
- Especially within cities, the GPS position might "jump", due to bad reception
caused by tunnels, underground garages or reflections on houses etc.
- A trip should be considered as started, as soon as the vehicle starts moving 
- A trip should be considered as ended, if the vehicle does not move for 
longer than 3 minutes

## The Program
The result of the coding challenge has to be a working program, which is able
to transform a stream of GPS waypoints into a list of Trip entities. It is up
to you, how you make the functionality available to the user, providing a CLI 
tool would be a good way.

The overall bootstrapping (e.g. providing command line functionality, parsing 
arguments, etc.) can be done with 3rd party libraries. The main focus and the 
only part that matters for the evaluation of the coding challenge is the 
implementation of the trip extraction logic.

### The Processor Interface
In the real world, there are different resources of waypoints, as we receive 
raw data from very different data providers. It could be a single large payload 
with all data of a certain time frame or the raw data could be a stream, 
where we receive data in real time, just as it happens.

Therefore for this coding challenge, you can choose between two different 
approaches, when implementing the data processor which handles trip extraction. 
They differ in terms of complexity and therefore in overall effort. 
It is up to you, which variant you choose to implement.

#### Interface A: List Processor
The first variant you can choose from is a List Processor. That means, on 
initialization the ListProcessor receives the full list of all waypoints. 
This list is held in memory, so the ListProcessor has access to the whole list 
of waypoints during the trip extraction process.

The List Processor provides a method, which returns the whole list of Trips, 
which is derived from the initially given list of Waypoints.

#### Interface B: Stream Processor
The second variant you can choose from is a Stream Processor. Instead of a list 
of Waypoints, the Stream Processor only receives one Waypoint at a time. The 
processor does not have access to the full list of waypoints.
If the Stream Processor recognizes a complete trip, the processor returns a 
Trip object, otherwise it returns None/null/void.

#### Python Example
In the file `processor.py` you can find an exemplary interface of the two 
processors described above.  


## Evaluation
Your contribution will be evaluated from different perspectives.
In our review we will focus on:
- accuracy – which trips are extraced from the raw data?
- quality – clean, tested implementation, which follows best practises?
- performance – how does the implementation behave with a much bigger dataset? 
