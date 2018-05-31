# spring_boot_rest

 ![spring](https://user-images.githubusercontent.com/28655112/40650279-d8539508-632a-11e8-95de-385ed029eb5a.png)
## Objectifs:
Construire une API REST dans Spring Boot

Il permet de rechercher un  ou tous les blogs, d'obtenir, de créer, de mettre à jour et de supprimer un blog existant.
## Exigences
Bonne compréhension du langage de programmation Java 

Connaissance de base de Maven
## Outils :
•	Java Development Kit (JDK) 1.7+

•	IntelliJ IDEA
## Les etapes :
•	Ouvrir "IntelliJ IDEA" et cliquez sur "Créer un nouveau projet"

 ![open](https://user-images.githubusercontent.com/28655112/40650884-39b19ce0-632c-11e8-931c-291cc6eb2b4f.png)

•	Sélectionnez "Projet Maven"

 ![capture3](https://user-images.githubusercontent.com/28655112/40651033-a5421f3e-632c-11e8-8037-7593a869aa11.PNG)

•	Remplisser les informations requises. "GroupId" est le nom d'organisation unique (dans la plupart des cas, les gens utilisent le nom de domaine de leur société tel que "com.axeane"), "ArtifactId" est le nom unique du projet, et "Version" est le numéro de version du projet

![capture2](https://user-images.githubusercontent.com/28655112/40651081-c27b3540-632c-11e8-83c8-0acfe824a218.PNG)
 
•	Après avoir terminer l'installation, on obtient un projet vide avec un fichier de configuration Maven "pom.xml", Maven est un gestionnaire de dépendances, Pour l'instant, nous avons besoin que de "Spring Boot". 
```bash	
  <parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>1.5.9.RELEASE</version>
</parent>
<dependencies>
 <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
 </dependency>
</dependencies>
<properties>
 <java.version>1.8</java.version>
</properties>
<build>
 <plugins>
    <plugin>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-maven-plugin</artifactId>
    </plugin>
 </plugins>
</build>
```
- La balise "< parent >" indique à Maven d'hériter des propriétés de "spring-boot-starter-parent" telles que le numéro de port, les configs, etc.
- La balise "< dependencies >" contient toutes les dépendances du projet. pour l'instant, nous n'avons qu'une dépendance "spring-boot-starter-web". Dans une application complète, il pourrait y avoir plus, par exemple. MySQL, Socket, bibliothèque JSON et
- Le "< build >" contient des plugins de construction tels que "spring-maven-plugin"

•	Pour qu'une application Java s'exécute, on doit disposer une "classe principale" nommée "MainApplicationClass.java"
```bash	
package axeane;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
@SpringBootApplication
public class MainApplicationClass {
    public static void main(String[] args) {
        SpringApplication.run(MainApplicationClass.class, args);
    }
}
```
Remarque : L'annotation "@SpringBootApplication" pour indiquer qu’il s’agit d’une application Spring Boot

##	Création d’un contrôleur : 
Le contrôleur gère toutes les demandes HTTP entrantes de l'utilisateur et renvoie une réponse appropriée..
```bash
package axeane;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
@RestController
public class BlogController {
    @RequestMapping("/")
    public String index() {
        return "Congratulations from BlogController.java";
    }
}
```
L'annotation @RestController indique au Spring que cette classe est un contrôleur

L’ annotation @RequestMapping("/") signifie que toute demande (GET, POST, PUT etc) à l'URL racine (/) sera gérée par la méthode "index()". La réponse est de type "String".
 ![execution](https://user-images.githubusercontent.com/28655112/40651210-0696b7a4-632d-11e8-906f-2bf41da17431.png)
 
## Création du classe Blog:
On va créer la classe Blog contenant les propriétés du blog(id, title, content), et un constructeur par défaut et un constructeur paramétré et une méthode  "toString()" pour imprimer le contenu du classe.
package axeane;
```bash
import javax.persistence.*;

@Entity
public class Blog {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private int id;

    private String title;
    private String content;

    public Blog() {
    }

    public Blog(String title, String content) {
        this.setTitle(title);
        this.setContent(content);
    }

    public Blog(int id, String title, String content) {
        this.setId(id);
        this.setTitle(title);
        this.setContent(content);
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getTitle() {
        return title;
    }

    public void setTitle(String title) {
        this.title = title;
    }

    public String getContent() {
        return content;
    }

    public void setContent(String content) {
        this.content = content;
    }

    @Override
    public String toString() {
        return "Blog{" +
                "id=" + id +
                ", title='" + title + '\'' +
                ", content='" + content + '\'' +
                '}';
    }
}
 ```
##	Création des données du blog : 
C'est la création d'une classe pour simuler la fonctionnalité de base de données qui sera créée et remplacée plus tard par la mise en œuvre réelle

La classe doit être un singleton afin que les modifications puissent être conservées dans différentes requêtes http en créant une méthode statique qui renvoie une instance de cette classe.
```bash
private static BlogMockedData instance = null;
public static BlogMockedData getInstance(){
   if(instance == null){
       instance = new BlogMockedData();
   }
   return instance;
}
```
Donc, chaque fois que nous avons besoin d'une instance de "BlogMockedData", nous l'instancions comme ceci:
```bash
BlogMockedData blogMockedData = BlogMockedData.getInstance();
```

Et non : 

```bash 
BlogMockedData  blogMockedData = new BlogMockedData(); 
```

```bash
public class BlogMockedData {
    //list of blog posts
    private List<Blog> blogs;

    private static BlogMockedData instance = null;
    public static BlogMockedData getInstance(){
        if(instance == null){
            instance = new BlogMockedData();
        }
        return instance;
    }
    public BlogMockedData(){
        blogs = new ArrayList<Blog>();
        blogs.add(new Blog(1, "Go up, up and away with your Google Assistant",
                "With holiday travel coming up, and 2018 just around the corner, " +
                        "you may be already thinking about getaways for next year. Consider " +
                        "the Google Assistant your new travel buddy, ready at the drop of a hat—or a passport"));
        blogs.add(new Blog(2, "Get local help with your Google Assistant",
                "No matter what questions you’re asking—whether about local traffic or " +
                        "a local business—your Google Assistant should be able to help. And starting " +
                        "today, it’s getting better at helping you, if you’re looking for nearby services " +
                        "like an electrician, plumber, house cleaner and more"));
        blogs.add(new Blog(3, "The new maker toolkit: IoT, AI and Google Cloud Platform",
                "Voice interaction is everywhere these days—via phones, TVs, laptops and smart home devices " +
                        "that use technology like the Google Assistant. And with the availability of maker-friendly " +
                        "offerings like Google AIY’s Voice Kit, the maker community has been getting in on the action " +
                        "and adding voice to their Internet of Things (IoT) projects."));
        blogs.add(new Blog(4, "Learn more about the world around you with Google Lens and the Assistant",
                "Looking at a landmark and not sure what it is? Interested in learning more about a movie as " +
                        "you stroll by the poster? With Google Lens and your Google Assistant, you now have a helpful " +
                        "sidekick to tell you more about what’s around you, right on your Pixel."));
        blogs.add(new Blog(5, "7 ways the Assistant can help you get ready for Turkey Day",
                "Thanksgiving is just a few days away and, as always, your Google Assistant is ready to help. " +
                        "So while the turkey cooks and the family gathers, here are some questions to ask your Assistant."));
    }
    // return all blogs
    public List<Blog> fetchBlogs() {
        return blogs;
    }
    // return blog by id
    public Blog getBlogById(int id) {
        for(Blog b: blogs) {
            if(b.getId() == id) {
                return b;
            }
        }
        return null;
    }
    // search blog by text
    public List<Blog> searchBlogs(String searchTerm) {
        List<Blog> searchedBlogs = new ArrayList<Blog>();
        for(Blog b: blogs) {
            if(b.getTitle().toLowerCase().contains(searchTerm.toLowerCase()) ||
                    b.getContent().toLowerCase().contains(searchTerm.toLowerCase())) {
                searchedBlogs.add(b);
            }
        }
        return searchedBlogs;
    }
    // create blog
    public Blog createBlog(int id, String title, String content) {
        Blog newBlog = new Blog(id, title, content);
        blogs.add(newBlog);
        return newBlog;
    }
    // update blog
    public Blog updateBlog(int id, String title, String content) {
        for(Blog b: blogs) {
            if(b.getId() == id) {
                int blogIndex = blogs.indexOf(b);
                b.setTitle(title);
                b.setContent(content);
                blogs.set(blogIndex, b);
                return b;
            }
        }
        return null;
    }
    // delete blog by id
    public boolean delete(int id){
        int blogIndex = -1;
        for(Blog b: blogs) {
            if(b.getId() == id) {
                blogIndex = blogs.indexOf(b);
                continue;
            }
        }
        if(blogIndex > -1){
            blogs.remove(blogIndex);
        }
        return true;
    }
}
```
### Manipulation de différents types de demandes

@RequestMapping - Pour gérer tout type de requête

@GetMapping - Pour la requête GET 

@PostMapping - Pour la requête POST 

@PutMapping - pour une requête PUT 

@PatchMapping - pour la requête PATCH 

@DeleteMapping pour la requête DELETE

Pour effectuer une requête POST, la méthode traitant la requête sera annotée comme suit:
```bash
@RequestMapping(value = "path", method = RequestMethod.POST)
public returnType methodName(){...}
```
Ou comme ceci:
```bash
@PostMapping("path")
public returnType methodName(){...}
```
### Path Variable et Request Parameters

![param](https://user-images.githubusercontent.com/28655112/40651545-e39438ca-632d-11e8-9bcc-bc1a669459ce.png)
 
Les Path Variable sont des variables dans l'URL de la requête et annotées avec "@PathVariable", par exemple id.
```bash
@GetMapping("/blog/{id}")
public returnType methodName(@PathVariable String id)
```
Les paramètres de requête sont des paires clé-valeur dans l'URL de requête avec l'annotation "@RequestParam", par exemple param1 et param2.
```bash
@GetMapping("/blog")
public returnType methodName(@RequestParam String param1, @RequestParam String param2)
```
### Request Body
La requête peut contenir un contenu utile appelé " RequestBody " annoté par  @RequestBody qui contient les données qui pourraient être stockées ou mises à jour et généralement au format JSON
```bash
@PostMapping("/blog")
public Blog create(@RequestBody Map<String, String> body){...}
```
## Le Controlleur
```bash
package axeane;
import org.springframework.web.bind.annotation.*;
import java.util.List;
import java.util.Map;
@RestController
public class BlogController {
    BlogMockedData blogMockedData = BlogMockedData.getInstance();
    @GetMapping("/blog")
    public List<Blog> index(){
        return blogMockedData.fetchBlogs();
    }
    @GetMapping("/blog/{id}")
    public Blog show(@PathVariable String id){
        int blogId = Integer.parseInt(id);
        return blogMockedData.getBlogById(blogId);
    }
    @PostMapping("/blog/search")
    public List<Blog> search(@RequestBody Map<String, String> body){
        String searchTerm = body.get("text");
        return blogMockedData.searchBlogs(searchTerm);
    }
    @PostMapping("/blog")
    public Blog create(@RequestBody Map<String, String> body){
        int id = Integer.parseInt(body.get("id"));
        String title = body.get("title");
        String content = body.get("content");
        return blogMockedData.createBlog(id, title, content);
    }
    @PutMapping("/blog/{id}")
    public Blog update(@PathVariable String id, @RequestBody Map<String, String> body){
        int blogId = Integer.parseInt(id);
        String title = body.get("title");
        String content = body.get("content");
        return blogMockedData.updateBlog(blogId, title, content);
    }
    @DeleteMapping("blog/{id}")
    public boolean delete(@PathVariable String id){
        int blogId = Integer.parseInt(id);
        return blogMockedData.delete(blogId);
    }
}
```
## L’exécution des requêtes en Postman

 ![capture](https://user-images.githubusercontent.com/28655112/40651299-4a507034-632d-11e8-9605-3f9cf0211db7.PNG)
