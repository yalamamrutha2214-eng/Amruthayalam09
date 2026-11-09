import java.util.*;
import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;

class Course {
    int id;
    String name;

    Course(int id, String name) {
        this.id = id;
        this.name = name;
    }
}

class Student {
    int id;
    String name;
    int courseId;

    Student(int id, String name, int courseId) {
        this.id = id;
        this.name = name;
        this.courseId = courseId;
    }
}

class Assignment {
    int id;
    String title;
    int courseId;
    LocalDateTime deadline;

    Assignment(int id, String title, int courseId,
               LocalDateTime deadline) {
        this.id = id;
        this.title = title;
        this.courseId = courseId;
        this.deadline = deadline;
    }
}

class Submission {
    int studentId;
    int assignmentId;
    String content;
    LocalDateTime submittedTime;

    Submission(int studentId, int assignmentId,
               String content, LocalDateTime time) {
        this.studentId = studentId;
        this.assignmentId = assignmentId;
        this.content = content;
        this.submittedTime = time;
    }
}

public class AssignmentTracker {

    static Scanner sc = new Scanner(System.in);

    static ArrayList<Course> courses = new ArrayList<>();
    static ArrayList<Student> students = new ArrayList<>();
    static ArrayList<Assignment> assignments = new ArrayList<>();
    static ArrayList<Submission> submissions = new ArrayList<>();

    static int courseId = 1;
    static int studentId = 1;
    static int assignmentId = 1;

    static DateTimeFormatter format =
        DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm");

    public static void main(String[] args) {

        while (true) {
            System.out.println("\n===== STUDENT ASSIGNMENT TRACKER =====");
            System.out.println("1. Add Course");
            System.out.println("2. Add Student");
            System.out.println("3. Create Assignment");
            System.out.println("4. Submit Assignment");
            System.out.println("5. Track Submissions");
            System.out.println("6. Exit");

            System.out.print("Enter Your Choice: ");
            int choice = sc.nextInt();
            sc.nextLine();

            switch (choice) {
                case 1:
                    addCourse();
                    break;
                case 2:
                    addStudent();
                    break;
                case 3:
                    createAssignment();
                    break;
                case 4:
                    submitAssignment();
                    break;
                case 5:
                    trackSubmissions();
                    break;
                case 6:
                    System.out.println("Thank You!");
                    return;
                default:
                    System.out.println("Invalid Choice!");
            }
        }
    }

    // 1. ADD COURSE
    static void addCourse() {
        System.out.print("Enter Course Name: ");
        String name = sc.nextLine();

        courses.add(new Course(courseId++, name));

        System.out.println("Course Added Successfully!");
    }

    // 2. ADD STUDENT
    static void addStudent() {
        System.out.print("Enter Student Name: ");
        String name = sc.nextLine();

        System.out.print("Enter Course ID: ");
        int cid = sc.nextInt();

        if (!courseExists(cid)) {
            System.out.println("Course Not Found!");
            return;
        }

        students.add(new Student(studentId++, name, cid));

        System.out.println("Student Added Successfully!");
    }

    // 3. CREATE ASSIGNMENT WITH DEADLINE
    static void createAssignment() {
        System.out.print("Enter Assignment Title: ");
        String title = sc.nextLine();

        System.out.print("Enter Course ID: ");
        int cid = sc.nextInt();
        sc.nextLine();

        if (!courseExists(cid)) {
            System.out.println("Course Not Found!");
            return;
        }

        System.out.print("Enter Deadline (yyyy-MM-dd HH:mm): ");
        LocalDateTime deadline =
            LocalDateTime.parse(sc.nextLine(), format);

        assignments.add(
            new Assignment(assignmentId++, title, cid, deadline)
        );

        System.out.println("Assignment Created Successfully!");
    }

    // 4. SUBMIT ASSIGNMENT
    static void submitAssignment() {
        System.out.print("Enter Student ID: ");
        int sid = sc.nextInt();

        System.out.print("Enter Assignment ID: ");
        int aid = sc.nextInt();
        sc.nextLine();

        Student student = findStudent(sid);
        Assignment assignment = findAssignment(aid);

        if (student == null || assignment == null ||
            student.courseId != assignment.courseId) {
            System.out.println("Invalid Student or Assignment!");
            return;
        }

        for (Submission sub : submissions) {
            if (sub.studentId == sid &&
                sub.assignmentId == aid) {
                System.out.println("Already Submitted!");
                return;
            }
        }

        System.out.print("Enter Assignment Text or Link: ");
        String content = sc.nextLine();

        submissions.add(new Submission(
            sid, aid, content, LocalDateTime.now()
        ));

        System.out.println("Assignment Submitted Successfully!");
    }

    // 5. TRACK SUBMISSIONS AND LATE WORK
    static void trackSubmissions() {
        System.out.print("Enter Assignment ID: ");
        int aid = sc.nextInt();

        Assignment assignment = findAssignment(aid);

        if (assignment == null) {
            System.out.println("Assignment Not Found!");
            return;
        }

        System.out.println("\nAssignment: " + assignment.title);
        System.out.println("Deadline: " +
            assignment.deadline.format(format));

        for (Student s : students) {

            if (s.courseId != assignment.courseId)
                continue;

            System.out.println("\nStudent: " + s.name);

            Submission found = null;

            for (Submission sub : submissions) {
                if (sub.studentId == s.id &&
                    sub.assignmentId == aid) {
                    found = sub;
                    break;
                }
            }

            if (found == null) {
                System.out.println("Status: PENDING");
            } else {
                System.out.println("Status: SUBMITTED");

                System.out.println("Submission Time: " +
                    found.submittedTime.format(format));

                if (found.submittedTime.isAfter(
                    assignment.deadline)) {
                    System.out.println("Late Work: LATE");
                } else {
                    System.out.println("Late Work: ON TIME");
                }

                System.out.println("Submitted Work: " +
                    found.content);
            }
        }
    }

    // CHECK COURSE
    static boolean courseExists(int id) {
        for (Course c : courses) {
            if (c.id == id)
                return true;
        }
        return false;
    }

    // FIND STUDENT
    static Student findStudent(int id) {
        for (Student s : students) {
            if (s.id == id)
                return s;
        }
        return null;
    }

    // FIND ASSIGNMENT
    static Assignment findAssignment(int id) {
        for (Assignment a : assignments) {
            if (a.id == id)
                return a;
        }
        return null;
    }
}
