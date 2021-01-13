**`© 2021 | Sammidev`**


**`CONTOH 1`**

**`master buku`**
``` sql
CREATE TABLE m_buku (
  id  INT,
  isbn VARCHAR(255) NOT NULL,
  judul VARCHAR(255) NOT NULL,
  pengarang VARCHAR(45) NOT NULL,
  PRIMARY KEY (id),
  UNIQUE (isbn)
);
```

**`master anggota`**
``` sql
CREATE TABLE m_anggota (
  id INT,
  no_anggota VARCHAR(255) NOT NULL,
  judul VARCHAR(255) NOT NULL,
  nama VARCHAR(45) NOT NULL,
  email VARCHAR(45) NOT NULL,
  PRIMARY KEY (id),
  UNIQUE (email)
);
```

**`transaction peminjaman header`**
``` sql
CREATE TABLE t_peminjaman_header (
  id INT,
  kode_transksi VARCHAR(255) NOT NULL,
  waktu_transaksi DATETIME,
  id_anggota INT,
  PRIMARY KEY (id),
  UNIQUE (email),
  FOREIGN KEY (id_anggota) REFERENCES m_anggota (id)
);
```

**`transaction peminjaman detail`**
``` sql
CREATE TABLE t_peminjaman_detail (
  id INT,
  t_peminjaman_header_id INT,
  id_buku INT,
  PRIMARY KEY (id),
  FOREIGN KEY (t_peminjaman_header_id) REFERENCES t_peminjaman_header (id),
  FOREIGN KEY (id_buku) REFERENCES m_buku (id),
);
```

**`CONTOH 2`**

**`master provinsi`**
``` sql
CREATE TABLE m_provinsi (
  id   VARCHAR(36),
  nama VARCHAR(255) NOT NULL,
  PRIMARY KEY (id),
  UNIQUE (nama)
);
```

**`master kabupaten kota`**
``` sql
CREATE TABLE m_kabupaten_kota (
  id          VARCHAR(36),
  id_provinsi VARCHAR(36) NOT NULL,
  nama VARCHAR (255) NOT NULL,
  PRIMARY KEY (id),
  UNIQUE (nama),
  FOREIGN KEY (id_provinsi) REFERENCES m_provinsi (id)
);
```

**`master kecamatan`**
``` sql
CREATE TABLE m_kecamatan (
  id                VARCHAR(36),
  id_kabupaten_kota VARCHAR(36)  NOT NULL,
  nama              VARCHAR(255) NOT NULL,
  PRIMARY KEY (id),
  UNIQUE (nama),
  FOREIGN KEY (id_kabupaten_kota) REFERENCES m_kabupaten_kota (id)
);
```

**`master kelurahan`**
``` sql
CREATE TABLE m_kelurahan (
  id           VARCHAR(36),
  id_kecamatan VARCHAR(36)  NOT NULL,
  nama         VARCHAR(255) NOT NULL,
  kodepos      VARCHAR(10)  NOT NULL,
  PRIMARY KEY (id),
  UNIQUE (nama),
  FOREIGN KEY (id_kecamatan) REFERENCES kecamatan (id)
);
```

**`transaction registrasi`**
``` sql
CREATE TABLE t_registrasi (
  id        VARCHAR(36),
  email     VARCHAR(255) NOT NULL,
  password  VARCHAR(255) NOT NULL,
  nama      VARCHAR(255) NOT NULL,
  alamat    VARCHAR(255) NOT NULL,
  id_kelurahan VARCHAR(36) NOT NULL,
  PRIMARY KEY (id),
  FOREIGN KEY (id_kelurahan) REFERENCES m_kelurahan(id)
);
```

**`data sample`**
``` sql
INSERT INTO m_provinsi (id, nama) VALUES ('dki', 'DKI Jakarta');
INSERT INTO m_provinsi (id, nama) VALUES ('jatim', 'Jawa Timur');
INSERT INTO m_provinsi (id, nama) VALUES ('jabar', 'Jawa Barat');

INSERT INTO m_kabupaten_kota (id, id_provinsi, nama) VALUES ('kabbogor', 'jabar', 'Kabupaten Bogor');
INSERT INTO m_kabupaten_kota (id, id_provinsi, nama) VALUES ('kotabogor', 'jabar', 'Kota Bogor');
INSERT INTO m_kabupaten_kota (id, id_provinsi, nama) VALUES ('surabaya', 'jatim', 'Surabaya');
INSERT INTO m_kabupaten_kota (id, id_provinsi, nama) VALUES ('mojokerto', 'jatim', 'Mojokerto');

INSERT INTO m_kecamatan (id, id_kabupaten_kota, nama) VALUES ('cibinong', 'kabbogor', 'Cibinong');
INSERT INTO m_kecamatan (id, id_kabupaten_kota, nama) VALUES ('gnputri', 'kabbogor', 'Gunung Putri');
INSERT INTO m_kecamatan (id, id_kabupaten_kota, nama) VALUES ('bootimur', 'kotabogor', 'Bogor Timur');
INSERT INTO m_kecamatan (id, id_kabupaten_kota, nama) VALUES ('boobarat', 'kotabogor', 'Bogor Barat');
INSERT INTO m_kecamatan (id, id_kabupaten_kota, nama) VALUES ('rungkut', 'surabaya', 'Rungkut');
INSERT INTO m_kecamatan (id, id_kabupaten_kota, nama) VALUES ('wonokromo', 'surabaya', 'Wonokromo');
INSERT INTO m_kecamatan (id, id_kabupaten_kota, nama) VALUES ('mojosari', 'mojokerto', 'Mojosari');
INSERT INTO m_kecamatan (id, id_kabupaten_kota, nama) VALUES ('trowulan', 'mojokerto', 'Trowulan');

INSERT INTO m_kelurahan (id, id_kecamatan, nama, kodepos) VALUES ('tengah', 'cibinong', 'Tengah', '16914');
INSERT INTO m_kelurahan (id, id_kecamatan, nama, kodepos) VALUES ('pakansari', 'cibinong', 'Pakansari', '16915');
INSERT INTO m_kelurahan (id, id_kecamatan, nama, kodepos) VALUES ('ciangsana', 'gnputri', 'Ciangsana', '16968');
INSERT INTO m_kelurahan (id, id_kecamatan, nama, kodepos) VALUES ('cikeas', 'gnputri', 'Cikeas', '16966');
```


**`CONTOH KODE`**

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
