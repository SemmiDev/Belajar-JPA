**`JPA`**
**`© 2021 | Sammidev`**

```java
import lombok.Data;
import javax.persistence.*;

@Data
@Entity
@Table(
        name = "student",
        uniqueConstraints = {
                @UniqueConstraint(name = "student_email_unique", columnNames = "email")
        }
)
public class Student {
    @Id
    @SequenceGenerator(name = "student_sequence", sequenceName = "student_sequence", allocationSize = 1)
    @GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "student_sequence")
    @Column(name = "id", updatable = false)
    private Long id;

    @Column(name = "first_name", nullable = false, columnDefinition = "TEXT")
    private String firstName;

    @Column(name = "last_name", nullable = false, columnDefinition = "TEXT")
    private String lastName;

    @Column(name = "email", nullable = false, columnDefinition = "TEXT")
    private String email;

    @Column(name = "phone")
    private String phone;
    
    @Column(name = "age", nullable = false)
    private Integer age;
}
```
```java
import lombok.Data;
import org.hibernate.annotations.GenericGenerator;

import javax.persistence.*;
import javax.validation.constraints.NotEmpty;
import javax.validation.constraints.NotNull;
import javax.validation.constraints.Size;
import java.util.ArrayList;
import java.util.Date;
import java.util.List;

@Data
@Entity
@Table(name = "student")
public class Student {

    @Id @GeneratedValue(generator = "uuid")
    @GenericGenerator(name = "uuid", strategy = "uuid2")
    private String id;

    @NotNull @NotEmpty @Size(min = 3, max = 50)
    @Column(name = "name", nullable = false)
    private String name;

    @NotNull @NotEmpty @Size(min = 10, max = 10)
    @Column(name = "nim", nullable = false, unique = true)
    private String nim;

    @Column(name = "email", nullable = false, unique = true)
    private String email;

    @OneToMany(mappedBy = "student")
    private List<Matkul> matkulList = new ArrayList<>();
}
```

```java
@Data
@Entity
@Table(name = "matkul")
class Matkul {

    @Id @GeneratedValue(generator = "uuid")
    @GenericGenerator(name = "uuid", strategy = "uuid2")
    private String id;

    @NotNull @NotEmpty @Size(min = 3, max = 50)
    @Column(name = "matkul", nullable = false)
    private String matkul;

    @NotNull @NotEmpty @Size(min = 3, max = 50)
    @Column(name = "lecture", nullable = false)
    private String lecture;

    @ManyToOne
    @JoinColumn(name = "id_student", nullable = false)
    private Student student;
}
```

```java
@Data
@Entity @Table(name="news")
class News {
    
    @Id @GeneratedValue
    private Integer id;

    @Column(name="time_publication", nullable=false)
    @Temporal(TemporalType.TIMESTAMP)
    private Date timePublication;

    @Column(nullable=false)
    private String title;

    @Column(nullable=false)
    private String content;
    
    @OneToMany
    @JoinTable(
            name="comment_news",
            joinColumns=@JoinColumn(name="id_news", nullable=false),
            inverseJoinColumns=@JoinColumn(name="id_comment", nullable=false)
    )
    private List<Comment> commentList = new ArrayList<>();
}
```

```java
@Data
@Entity
@Table(name="comment")
class Comment {
    @Id @GeneratedValue
    private Integer id;

    @Column(name="time_publication", nullable=false)
    @Temporal(TemporalType.TIMESTAMP)
    private Date timePublication;

    @Column(nullable=false)
    private String email;

    @Column(nullable=false)
    private String name;

    @Column(nullable=false)
    private String content;
    
    @ManyToOne
    @JoinTable(
            name = "comment_news",
            joinColumns = @JoinColumn(name = "id_comment", insertable = false, updatable = false),
            inverseJoinColumns = @JoinColumn(name = "id_news", insertable = false, updatable = false)
    )
    private News news;
}
```

**EXAMPLE**

**`entity`**

```java
@Builder
@Data
@AllArgsConstructor
@NoArgsConstructor
@Entity(name = "Student")
@Table(name = "student", uniqueConstraints = {@UniqueConstraint(name = "student_email_unique", columnNames = "email")})
class Student {
	@Id @SequenceGenerator(name = "student_sequence", sequenceName = "student_sequence", allocationSize = 1)
	@GeneratedValue(strategy =  SEQUENCE, generator = "student_sequence")
	@Column(name = "id", updatable = false)
	private Long id;

	@Column(name = "first_name", nullable = false, columnDefinition = "TEXT")
	private String firstName;

	@Column(name = "last_name", nullable = false, columnDefinition = "TEXT")
	private String lastName;

	@Column(name = "email", nullable = false, columnDefinition = "TEXT")
	private String email;

	@Column(name = "age", nullable = false)
	private Integer age;
}
```
**`repository`**

```java
interface StudentRepository extends JpaRepository<Student, Long>{}
```

**`main`**
```java
// © 2021 sammidev

package com.sammidev;

import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;
import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;
import org.springframework.data.jpa.repository.JpaRepository;
import javax.persistence.*;
import java.util.List;
import static javax.persistence.GenerationType.SEQUENCE;

@SpringBootApplication
public class LearnJpaApplication {

	public static void main(String[] args) {
		SpringApplication.run(LearnJpaApplication.class, args);
	}

	@Bean
	CommandLineRunner commandLineRunner(StudentRepository studentRepository) {
		return args -> {
			Student sammi = Student.builder()
					.firstName("sammi")
					.lastName("dev")
					.email("sammidev@gmail.com")
					.age(19).build();
			Student ayatullah = Student.builder()
					.firstName("ayat")
					.lastName("ullah")
					.email("ayatullah@gmail.com")
					.age(80).build();
			studentRepository.saveAll(List.of(sammi, ayatullah));
			var countStudent = studentRepository.count();

			studentRepository
					.findById(2L)
					.ifPresentOrElse(
							System.out::println,
							() -> System.out.println("Student with ID 2 not found"));
			studentRepository
					.findById(3L)
					.ifPresentOrElse(
							System.out::println,
							() -> System.out.println("Student with ID 3 not found"));


			System.out.println("Select all students");
			List<Student> students = studentRepository.findAll();
			students.forEach(System.out::println);

			System.out.println("Delete maria");
			studentRepository.deleteById(1L);

			System.out.print("Number of students: ");
			System.out.println(studentRepository.count());
		};
	}
}
```

**`properties`**
```properties
spring.datasource.url=jdcb:postgresql://localhost:5432/postgres
spring.datasource.username=postgres
spring.datasource.password=sammidev
spring.jpa.hibernate.ddl-auto=create-drop
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.PostgreSQLDialect
spring.jpa.properties.hibernate.format_sql=true
```