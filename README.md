# Cycle-to-Work-Aggregation-Project
Analysis on Cycle to Work data.

## Description

## init_db.ipynb
 Initializes the various tables and indexes within the database - "gps_heatmap". It includes the following table:
 
 1) **roads** - The roads table will hold the raw road data imported from the Bengaluru city's shapefile, this table will have three separate fields:
           
            • id: This is the unique ID for this road in the database.
            
            • name: This is the name of the road.
            
            • centerline: This is a LineString geometry that represents the shape of this road.
            
 2) **road_segments** - Each road segment will have the following fields:
 
                  • id: This is the unique ID for this road segment.
                  
                  • name: This is the name of the road this segment is a part of.
                  
                  • centerline: This is a LineString geometry representing the shape of this road segment.
                  
                  • tally: This is the number of times this road segment was used by the GPS recordings. 
                  
 3) **endpoints** - Each endpoint record will have the following fields:
 
              • id: This is the unique ID for this endpoint
              
              • endpoint: This is a Point geometry containing the coordinates for this endpoint
              
 4) **endpoint_segments** -  This table, endpoint_segments, will have the following fields:
 
                        • id: This is the unique ID for this endpoint_segments record.
                        
                        • directed_segment_id: This is the record ID of a directed road segment.
                        
                        • endpoint_id: This is the record ID of the endpoint that this directed roadsegment leaves from.
                        
 5) **directed_segments** - Each directed segment record will have the following fields:
 
                      • id: This is the unique ID for this road segment
                      
                      • road_segment_id: This is the record ID of the road segment this directed segment is derived from.
                      
                      • centerline: This is the LineString geometry for this directed segment 

 ## import_roads.ipynb
  Import the road data from the Bengaluru city's shapefile into the database
  
 ## split_roads.ipynb
   The points where roads touch or cross aren't automatically considered to be connection points for the purpose of building a road network.
   Hence,a planar graph out of the intersecting roads was created. The split_roads.py program will split the raw road data into segments,
   forming a planar graph of road segments out of the raw road data.
   
 ## calc_directed_network.ipynb
   Calculate the directed road network and store it into the database which is needed to find out which other road
   segments lead off from a particular segment.
   
 ## map_matcher.ipynb
   Implements the map matching algorithm.
   
 ## Idea behind the map matching algorithm
   
   This algorithm is based on the notion of a route candidate, which is a possible path the traveler could have taken as the GPS points were
   recorded. Each route candidate has a list of directed road segments and a score identifying how closely the GPS points match those road segments.
   
   The journey is recreated by following the GPS points one at a time. At any particular moment, there is a list of route candidates which could possibly
   match the GPS coordinates which have been processed thus far. As each new GPS point is processed, the route candidates are updated one at a time 
   by comparing the GPS coordinate with the route candidate's final road segment. 
   
   If the GPS point is considered to still be somewhere on that final segment, then the GPS point is simply added to that segment and the route 
   candidate's score is updated. If, on the other hand, the GPS point is beyond the end of the route candidate's final road segment, then we look at 
   the road network to see which other road segments lead off from that point. We then create new route candidates for each road segment
   leaving from that endpoint.
   
   Once all the GPS points have been processed, we select the route candidate with the lowest score(directly proportional to 
   the distance between the point and the segment) as being the  one most likely to have been used for this journey.
   
    Python dictionaries is used to represent a route segment—that is, a single segment within a route candidate's journey. Each route 
    segment dictionary will have the following entries:
    
    • directed_segment_id: This is the record ID of the directed_segment which this segment of the route is following.
    
    • linestring: This is the road segment's centerline, as a Shapely LineString object.
    
    • gps_points: This is a list of (long,lat) coordinates defining the GPS points which have been assigned to this route segment.
    
    • gps_distances: This is a list holding the calculated minimum distance between each GPS point and the segment's LineString.
    
    
    Each route candidate is going to be represented by a Python dictionary with the following entries:
    
    • segments: This is a list of the route segments that make up this route candidate.
    
    • directed_segment_ids: This is a list containing the record ID of each directed segment used by this route. 
      This is used to quickly discard a new route candidate if another route is using the same sequence of road segments.
      
    • score: This is the calculated score for this route candidate. The score is calculated as the sum of the GPS distances 
      in each route segment—in other words, the lower the score, the more closely the GPS points follow this route.
      

   
   The Google drive link to the backup of database: [gps_heatmap](https://drive.google.com/file/d/1vx1Ju3IPaYHgE3PD2KfN7bA6HHmqUxTN/view?usp=sharing)
    
    


   
