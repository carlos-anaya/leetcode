# 207. Course Schedule

There are a total of `numCourses` courses you have to take, labeled from 0 to numCourses-1.
Some courses may have *prerequisites*, for example to take course 0 you have to first take course 1,
which is expressed as a pair: **[0,1]**
Given the total number of courses and a list of prerequisite pairs, is it possible for you to 
*finish all courses*?

- Example 1:
> **Input**: numCourses = 3, prerequisites = [[1,0], [2,1]] <br>
> **Output**: True <br>
> **Explanation**: Take course 0, then course 1 and then course 2
- Example 2:
> **Input**: numCourses = 2, prerequisites = [[1,0],[0,1]] <br>
> **Output**: False <br>
> **Explanation**: Course 1 depends on course 0, course 0 depends on course 1, so it's not possible

## Optimal
- First, we need to process the input. We can deal with *int and bool arrays*, but let's define a 
  struct/object *Course* instead. The course has a *number*, *n subcourses*, and two flags: 
  *visited* and *in_process*.
- The output of the processing step is a `Map<Integer, Course>`
- Loop through all of the courses and use a *DFS recursive function*. Skip visited courses.
- Under what circumstances it is not possible to end all the courses? When there's a **cycle**.
  To prevent an infinite loop in the DFS function, we check if the course is currently *in process*,
  if so, then there's a cycle, so we return false.
- If a course is not in progress, then we mark it in progress and process it's children. Then, we
  mark the course as *visited*, and restore back *in_process* to false.

**Time: O(V+E) <br> Space: O(V+E)**

### Go solution
```go
type Course struct {
    courseNumber int
    subCourses []int
    visited bool
    inProcess bool
}

func canFinish(numCourses int, prerequisites [][]int) bool {
    coursesMap := buildCoursesMap(numCourses, prerequisites)
    
    for _, course := range coursesMap {
        if course.visited {
            continue
        }
        if !dfs(course, &coursesMap) {
            return false
        }
    }
    
    return true
}

func dfs(course *Course, coursesMap *map[int]*Course) bool {
    if course.inProcess {
        return false
    }
    
    course.inProcess = true
    for _, num := range course.subCourses {
        subCourse := (*coursesMap)[num]
        if subCourse.visited {
            continue
        }
        if !dfs(subCourse, coursesMap) {
            return false
        }
    }
    course.inProcess = false
    course.visited = true
    
    return true
}

func buildCoursesMap(num int, prereqs[][]int) map[int]*Course {
    coursesMap := make(map[int]*Course)
    
    for i := 0; i < num; i++ {
        arr := make([]int, 0)
        coursesMap[i] = &Course{courseNumber: i, subCourses: arr}
    }
    for _, prereq := range prereqs {
        course := coursesMap[prereq[1]]
        course.subCourses = append(course.subCourses, prereq[0])
    }
    
    return coursesMap
}

```
### Java solution
```java
class Solution {
    class Course {
        int number;
        List<Integer> subcourses;
        boolean taken;
        boolean inProcess;
    }
    public boolean canFinish(int numCourses, int[][] prerequisites) {
        Map<Integer, Course> coursesMap = buildCoursesMap(numCourses, prerequisites);
        
        for(int num : coursesMap.keySet()) {
            Course curr = coursesMap.get(num);
            if(curr.taken)
                continue;
            
            if(!dfs(curr, coursesMap))
                return false;
        }
        
        return true;
    }
    
    private boolean dfs(Course course, Map<Integer, Course> coursesMap) {
        if(course.inProcess)
            return false;
        
        course.inProcess = true;
        
        for(int sub : course.subcourses) {
            Course curr = coursesMap.get(sub);
            if(curr.taken)
                continue;
            
            if(!dfs(curr, coursesMap))
                return false;
        }
        
        course.taken = true;
        course.inProcess = false;
        return true;
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
        }
        
        return map;
    }
}
```