
An outline of my maze generating algorithm:
          
     This process will take an n by m integer matrix and procedurally fill it
     with paths and rooms, like a classic Rogue style maze. The algorithm is
     outlined by the following instructions:
    
     1) It is suffient to say that a room is defined by 2 points
        This is because each room, in this algorithm, is a rectangle.
        since rectangles have perpendicular sides,  we can spread out the sides
        above/below and left/right of these 2 corners to find the other 2.
        Therefore, a room can be stored as a 4 component array of integers
        where an the first two and second two numbers each refer to a position
        in the matrix titled maze. I will use the terms 4-component integer
        array and 4-vector interchangeably.
     
     2) For the first room, in the 0th and 2nd components of a 4-vector,
        assign random integers such that the difference of their values 
        is greater than 1 and they lie in the range from 0 to n - 1.Similarly, 
        in the first and 3rd components, assign values such that their 
        difference is greater than 1 and they lie in random from 0 to m - 1
        Ranges are inclusive. If our vector's components are written in the
        notation v = [v_0, ... , v_3] then this vector:
                               
                              v = [2, 5, 5, 7]
    
        Would equate to this room in a 10 by 10 matrix:
       
                           # # # # # # # # # #    'P' is the the location of 
                           # # # # # # # # # #    a generated point for a room
      '#'means empty       # # # # # P - C # #    
       space or Wall       # # # # # | . | # #    'C' repersents the connection
                           # # # # # | . | # #    of two generated points.
                           # # # # # C - P # #    
                           # # # # # # # # # #    '-' and '|' are horizontal
                           # # # # # # # # # #     and vertical connectors
                           # # # # # # # # # #     respectively. All non-'#'
                           # # # # # # # # # #     chars are walkable tiles
    
        Take note that these characters are not the literal value of the
        matrix storing the room. I am just using them as tiles for a
        visualization effect. There will be integers to represent the actual
        tiles.
    
        Each room vector is stored in a matrix, making a matrix where
        each row refers to a single room. this matrix has size 
        finalRooms by 4 and generating such a matrix is sufficient to creating
        all of the rooms necessary for a floor.
        
     3) Making any room after the first one requires a bit of extra tip-toing.
        We have two options, rooms can overlap or rooms may not overlap.Let us
        use this function to help us:
        
                      int inRoom(int point[2], int room[4]);
     
        It will return 1 if point is enclosed by Room, 0 otherwise.
        Note that in the pic above, anything point not labeled '#' returns 1.
        
        If we do not want rooms to overlap, we can use this function to set off
        a flag which prevents goes up if a given vector would cause any tiles
        from the rooms matrix to change tiles of older rooms in dungeon matrix
        See isStronglyConnected() below for further use .
        
        If we don't mind about overlaps, then no such extra processing is
        necessary.
      
     4) We now have a matrix filled with rooms which are are either overlapping
        or disconnected. If the rooms are overlapping, the we do not need to
        connect them through hallways.If any room is disjoint from all other
        rooms, then we must connect it so that one could traverse the whole
        maze.It will be convenient to keep track of which rooms are
        overlapping and which aren't.Define the following terms:
    
        Two columns of the finalRooms by 4 matrix are STRONGLY CONNECTED 
        if they produce overlapping rooms. Strong connectedness implies 
        connectedness:
    
        If two columns are not strongly connected, then one can CONNECT them
        by producing a point which is in neither room then connecting both
        rooms to that point. These rooms are called CONNECTED.
        
        Two rooms are connectedness is transitive. This property is stated so
        that we need not necessarily connect all rooms to one another to make
        a satisfactory maze. These functions will be useful:
    
        int isConnected(roomMatrix[][4], int room1, int room2);
        checks if the rooms are transitively connected. if they are, done.
        If they are not, then it checks if they are directly connected. 
        (through a connecting point/hallway or through strong connectedness)
        The int params there are the number row of the matrix which is a room.
        1 if connected, 0 otherwise.
    
        int isStronglyConnected(roomMatrix[][4], int room1, int room2);
        similar to above, but just checks if two rooms overlap.
        
        int connectRooms(roomMatrix[][4], int room1, int room2);
        checks if two rooms are connected, then connects them if they aren't.
        return 1 if connected successfull, 0 if already connected
    
        We use these functions to connect all the rooms. If one room is
        connected to every other room, then every room is connected to one
        another. (Since connectedness is transitive.)
        
        To connect all the rooms, use this loop:
        [1] -Check if room 0 is connected to every other room sequentially
                -if so, done
            -if not, pick two arbitrary rooms.check if connected, 
                -if they are, pick more arbitrary rooms until an unconnected 
                    pair is found
                -connect them
            -go back to line [1]
    
       Once all rooms are connected, our maze is done.
                                                                            QED
    
