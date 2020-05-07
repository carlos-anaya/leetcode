# 210. Course Schedule II

There are a total of `n` courses you have to take, labeled from **0 to n-1**.

Some courses may have prerequisites, for example to take course 0 you have to first take course 1,
which is expressed as a pair: *[0,1]*

Given the total number of courses and a list of prerequisite pairs, return the ordering of courses 
you should take to finish all courses.

There may be multiple correct orders, you just need to return one of them. If it is impossible to 
finish all courses, return an empty array.

- Example:
> **Input**: 4, [[1,0],[2,0],[3,1],[3,2]] <br>
> **Output**: [0,1,2,3] or [0,2,1,3] <br>
> **Explanation**: There are a total of 4 courses to take. To take course 3 you should have 
> finished both courses 1 and 2. Both courses 1 and 2 should be taken after you finished course 0.
> So one correct course order is [0,1,2,3]. Another correct ordering is [0,2,1,3] .

## Approach 1
We can follow the same approach as in [207-Course Schedule](207-Course%20Schedule.md). This is, 
using a **recursive DFS** function and marking the nodes as *visited* and *inProcess*. The only
difference is that the function would have a new parameter: **a list of courses**. After the
*subcourses* of a course have been processed, we can add the *current course* to the list.
Since we're adding the nodes processed to the list, then at the end we need to **revert** the list.

**Time: O(V+E) <br> Space: O(V+E)**

## Approach 2
This time we'll follow another approach. 
- We'll store the `in-degree` of each course and then, usinga **Queue** we'll keep track of the
courses that have *0 in-degree*, i.e. those courses that don't have a pre-requisite. 
- Then, we'll loop until the queue is empty. 
- For each course we take from the queue we'll check it's *sub-courses* and reduce by 1 their
*in-degree*, i.e. the current course is *"taken"* so the sub-courses have one less pre-requisite.
- If any of the subcourses now has a *0 in-degree*, we add it to the queue.
- If at the end, the number of taken courses is equal to the total courses, the path is valid (i.e.
the order is correct since we were taking courses that had 0 in-degree).

**Time: O(V+E)** (We have a loop for each course (V) and check it's subcourses (E))<br> 
**Space: O(V+E)** (The queue has V size, but the map (adjacency list) has V+E size).

### Go solution
```go
type Course struct {
    number int
    indegree int
    subcourses []int
}

func findOrder(numCourses int, prerequisites [][]int) []int {
    coursesMap := buildCoursesMap(numCourses, prerequisites)
    coursesQueue := NewQueue()
    
    for i := 0; i < numCourses; i++ {
        if coursesMap[i].indegree == 0 {
            coursesQueue.Enqueue(i)
        }
    }
    
    ans := make([]int, 0)
    for !coursesQueue.Empty() {
        curr := coursesQueue.Dequeue()
        for _, sub := range coursesMap[curr].subcourses {
            subcourse := coursesMap[sub]
            subcourse.indegree--
            
            if subcourse.indegree == 0 {
                coursesQueue.Enqueue(sub)
            }
        }
        ans = append(ans, curr)
    }
    
    if len(ans) == numCourses {
        return ans
    }
    return []int{}
}

func buildCoursesMap(numCourses int, prerequisites [][]int) map[int]*Course {
    coursesMap := make(map[int]*Course)
    for i := 0; i < numCourses; i++ {
        subcourses := make([]int, 0)
        coursesMap[i] = &Course{i, 0, subcourses}
    }
    for _, p := range prerequisites {
        coursesMap[p[1]].subcourses = append(coursesMap[p[1]].subcourses, p[0])
        coursesMap[p[0]].indegree++
    }
    
    return coursesMap
}
```
### Java solution
```java
class Solution {
    class Course {
        int number;
        ArrayList<Integer> subcourses;
        int inDegree;
    }
    
    public int[] findOrder(int numCourses, int[][] prerequisites) {
        Map<Integer, Course> coursesMap = buildCoursesMap(numCourses, prerequisites);
        Queue<Integer> zeroIndegreeCourses = new LinkedList();
        int[] ans = new int[numCourses];
        
        for(int num : coursesMap.keySet()) {
            if(coursesMap.get(num).inDegree == 0)
                zeroIndegreeCourses.add(num);
        }
        
        int idx = 0;
        while(!zeroIndegreeCourses.isEmpty()) {
            int courseNum = zeroIndegreeCourses.remove();
            for(int subNum : coursesMap.get(courseNum).subcourses) {
                Course subCourse = coursesMap.get(subNum);
                subCourse.inDegree--;
                if(subCourse.inDegree == 0)
                    zeroIndegreeCourses.add(subNum);
            }
            ans[idx++] = courseNum;
        }

        return idx == numCourses ? ans : new int[0];
    }
    
    private Map<Integer, Course> buildCoursesMap(int numCourses, int[][] prerequisites) {
        Map<Integer, Course> map = new HashMap();
        for(int i = 0; i < numCourses; i++) {
            Course course = new Course();
            course.number = i;
            course.subcourses = new ArrayList();
            map.put(i, course);
        }
        
        for(int[] prereq : prerequisites) {
            map.get(prereq[1]).subcourses.add(prereq[0]);
            map.get(prereq[0]).inDegree++;
        }
        
        return map;
    }
}
```