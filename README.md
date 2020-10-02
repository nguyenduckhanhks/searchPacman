# searchPacman
# python version: Python 2.7.18rc1
# Question 1 (3 points): Finding a Fixed Food Dot using Depth First Search
1. giải thuật:
    stack = []
    visited = []

    stack.append((problem.getStartState(),[]))
    while len(stack) > 0:
        top = stack.pop()
        if top[0] not in visited:
            visited.append(top[0])
            if(problem.isGoalState(top[0])):
                return top[1]
            for nextState in problem.getSuccessors(top[0]):
                stack.append((nextState[0], top[1] + [nextState[1]]))
                
 2. Chú thích :
  - stack: stack lưu các điểm cần xét (được xây dựng từ mảng )
      Các điểm được thêm vào stack có dạng: ((x,y),[array]) trong đó:
        + phần tử đầu tiên là vị trí điểm cần xét
        + array là mảng các hướng đi từ điểm ban đầu đến điểm đó
      => khi nếu điểm đó là đích sẽ return lại array này
  - visited: mảng lưu các điểm đã xét
 
 # Question 2 (3 points): Breadth First Search
    
    - Cách làm tương tự Question 1 nhưng thay stack = queue
    
 # Question 3 (3 points): Varying the Cost Function
 
    - Cách làm tương tự question 1 nhưng có một số điểm khác:
      + sử dụng pqueue thay cho stack
      + các điểm thêm vào pqueue có dữ liệu dạng: ((x,y),[array], h) trong đó:
        (x,y): toạ độ điểm cần xét
        array: mảng các hướng đi từ điểm ban đầu đến điểm đó
        h: giá trị cần để đi từ điểm ban đầu đến điểm đó
      + pqueue sẽ được sắp xếp theo giá trị h để xét mức độ ưu tiên
 
 # Question 4 (3 points): A* search
 
 - Cách làm tương tự question 1 nhưng có một số điểm khác:
      + sử dụng pqueue thay cho stack
      + các điểm thêm vào pqueue có dữ liệu dạng: ((x,y),[array], h, h+g) trong đó:
        (x,y): toạ độ điểm cần xét
        array: mảng các hướng đi từ điểm ban đầu đến điểm đó
        h: giá trị cần để đi từ điểm ban đầu đến điểm đó
        g: giá trị dự đoán nếu đi theo hướng đó để đến điểm đích
      + pqueue sẽ được sắp xếp theo giá trị h + g để xét mức độ ưu tiên
      
 # Question 5 (3 points): Finding All the Corners
 
    data trả về của hàm getStartState và getSuccessors sẽ có dạng ((x,y),[array]). Trong đó:
      + (x,y) toạ độ cảu điểm;
      + [array]: mảng các góc đã đi qua trước đó
    => tại một điểm xét; nếu [array] = 4 thì isGoalState = true
    
    * Hàm getSuccessors: 
      Duyệt qua 4 hướng
      Với mỗi hướng lấy ra điểm tiếp theo nếu theo hướng đó. nếu điểm đó là tường thì break
      Nếu điểm đó không phải tường thêm diểm đó vào mảng trả về với giá trị theo format trên. Trong đó:
        + [array] của điểm này được tính bằng [array] của điểm đang xét (append thêm điểm này nếu nó là góc)
     
     => giải thuật :
        successors = []
        cornerVisited = state[1]
        for action in [Directions.NORTH, Directions.SOUTH, Directions.EAST, Directions.WEST]:
            
            x,y = state[0]
            dx, dy = Actions.directionToVector(action)
            nextx, nexty = int(x + dx), int(y + dy)
            hitsWall = self.walls[nextx][nexty]
            if not hitsWall:
                newCornerVisited = cornerVisited
                if (nextx,nexty) in self.corners and (nextx,nexty) not in cornerVisited:
                    newCornerVisited = cornerVisited + [(nextx,nexty)]
                successors.append( (((nextx,nexty), newCornerVisited), action, 1) )

        self._expanded += 1
        return successors
        
 #Question 6 (3 points): Corners Problem: Heuristic
 
 1. Ý tưởng
 
    - Xây dựng một mảng distant giữa các điểm conner chưa đi qua với nhau và giữa các điểm food với điểm hiện tại đang xét.
    - Với mỗi hàng của mảng lấy ra khoảng cách nhỏ nhất khác 0 nằm trên đường chéo y = x của mảng. Nếu không có giá trị nào thoả mãn ta lấy giá trị 0
    - Cộng tất cả các giá trị tìm được của các hàng là giá trị cost cần tìm
    
 2. Giải thuật
 
    corners = problem.corners
    walls = problem.walls 
    arrCorner = [state[0]]
    for x in corners:
        if x not in state[1]:
            arrCorner.append(x)

    arrayDistance = []
    for x in range(0, len(arrCorner)):
        point1 = arrCorner[x]
        point1Distance = [] 

        for y in range(0, len(arrCorner)):
            point2 = arrCorner[y]
            distance = abs( point1[0] - point2[0] ) + abs( point1[1] - point2[1] )
            point1Distance.append(distance)

        arrayDistance.append( point1Distance )
    
    totalCost = 0.0
    for x in range(0, len(arrCorner)):
        minCost1 = 99999.0
        for y in range(x, len(arrCorner)):
            if arrayDistance[x][y] != 0 and arrayDistance[x][y] < minCost1:
                minCost1 = arrayDistance[x][y]
        if minCost1 == 99999.0:
            minCost1 = 0.0
        totalCost += minCost1
    return totalCost
    
  # Question 7 (4 points): Eating All The Dots
  
   1. Ý tưởng
 
    - Xây dựng một mảng distant giữa các điểm food chưa đi qua với nhau và giữa các điểm food với điểm hiện tại đang xét.
    - Với mỗi hàng của mảng lấy ra khoảng cách nhỏ nhất khác 0 nằm trên đường chéo y = x của mảng. Nếu không có giá trị nào thoả mãn ta lấy giá trị 0
    - Cộng tất cả các giá trị tìm được của các hàng
    - Nhận tổng trên vs hệ số alpha phù hợp là cost cần tìm (Trong bài chọn alpha = 5/6)
    
    2. giaỉ thuật:
    position, foodGrid = state
    arrFood = [(position[0], position[1])]
    for x in range(0, foodGrid.width):
        for y in range(0, foodGrid.height):
            if foodGrid[x][y]:
                arrFood.append((x,y))

    arrayDistance = []
    for x in range(0, len(arrFood)):
        point1 = arrFood[x]
        point1Distance = [] 

        for y in range(0, len(arrFood)):
            point2 = arrFood[y]
            distance = abs( point1[0] - point2[0] ) + abs( point1[1] - point2[1] )
            point1Distance.append(distance)

        arrayDistance.append( point1Distance )
    
    totalCost = 0.0
    for x in range(0, len(arrFood)):
        minCost1 = 99999.0
        for y in range(x, len(arrFood)):
            if arrayDistance[x][y] != 0 and arrayDistance[x][y] < minCost1:
                minCost1 = arrayDistance[x][y]
        if minCost1 == 99999.0:
            minCost1 = 0.0
        totalCost += minCost1
    return (totalCost * 5 / 6)
    
# Question 8 (3 points): Suboptimal Search
  Dựa trên ý tưởng bfs để làm bài này 
  1. giải thuật:
  
  queue = []
        visited = []

        queue.append((problem.getStartState(),[]))

        while len(queue) > 0:
            top = queue.pop(0)
            if top[0] not in visited:
                visited.append(top[0])
                if food[top[0][0]][top[0][1]]:
                    return top[1]
                for nextState in problem.getSuccessors(top[0]):
                    queue.append((nextState[0], top[1] + [nextState[1]]))
                    
                    
