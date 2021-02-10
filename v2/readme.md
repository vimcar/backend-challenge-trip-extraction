# Vimcar Coding Challenge: Trip Extraction

One of Vimcar's successful products is the digital logbook. The purpose
of a logbook is to maintain a complete, consistent list of all trips a 
vehicle has made over time. 

There are many different use-cases for this, one of them is taxation. 
Our customers e.g. maintain a logbook, to keep track of how many trips were 
made privately and how many kilometers were driven in a business context.


## The trip extraction

The central pice of the logbook is the trip extraction, the process of detecting movement 
in the gps data and determinging when a vehicle starts and stops moving.


When driving from A to B, a user will probably have interruptions like stopping at red lights, 
traffic jams, tunels.  The expected behaviour is to have one trip accounting for 
the whole journey, and not broken trips like from A to first red light etc.


GPS technology has some limitations which the trip extraction should account for. 
Within cities, the accuracy of GPS can be below 15 meters and the GPS position might even "jump", 
due to bad reception caused by reflections on building. There are places where is not gps signal
like tunels and undregraound parkings.


## The task

Your challange is to write a simplified version of the trip detection program.


For the trip extraction use the following logic:

- The trip start conditions is: a stopped vehicle leaves a circle of 20 meters diameter 
around it's rest position.
- The trip end conditions is: a moving vehicle remains within a circle of 20 meters 
diameter for longer than 5 minutes.
- Small distances of less than 10 meters between waypoints should be ignored.
- Large distances in a short time, more than 10 km/minute between waypoints should be also ignored.


The input is supplied in a file and contains of a list of Waypoints, this is the gps information 
collected for one vehicle. A waypoints has the following structure. 

- `timestamp` – string, a timestamp in ISO 8601 format
- `lat` – float, Latitude of the GPS coordinate (WGS84)
- `lng` – float, Longitude of the GPS coordinate (WGS84)

The output is a list of Trips. A trip contains the start and end locations and the distance driven.
A trip has the following structure. 

- `start` – object, the start of the trip in Waypoint schema (see above)
- `end` – object, the end of the trip in Waypoint schema (see above)
- `distance` – int, the distance of the trip in meters

The distance is the the sum of all distances between the individual waypoints of the trip and has to be 
calculated.


## Evaluation

The focus of the evaluation will be clean, readable and tested code, which follows best practises.
