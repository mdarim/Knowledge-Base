Here’s a complete guide to handling **file uploads** and **file downloads** in **Spring Boot 3** with best practices for **security, error handling,** and **file storage management**.

---

# 📤 **1. Handling File Uploads in Spring Boot**

Spring Boot provides built-in support for handling **multipart file uploads** using the **`MultipartFile`** interface.

### ✅ **Step 1: Add Dependencies in `build.gradle`**

Make sure your project has **Spring Web** dependency.

```groovy
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web'
}
```

---

### ✅ **Step 2: Create a Controller to Handle File Uploads**

```java
import org.springframework.web.bind.annotation.*;
import org.springframework.web.multipart.MultipartFile;

import java.io.File;
import java.io.IOException;

@RestController
@RequestMapping("/files")
public class FileUploadController {

    private static final String UPLOAD_DIR = "uploads/";

    // POST endpoint to handle file upload
    @PostMapping("/upload")
    public String uploadFile(@RequestParam("file") MultipartFile file) {
        try {
            // Create upload directory if it doesn't exist
            File uploadDir = new File(UPLOAD_DIR);
            if (!uploadDir.exists()) {
                uploadDir.mkdirs();
            }

            // Save the file to the upload directory
            file.transferTo(new File(UPLOAD_DIR + file.getOriginalFilename()));

            return "File uploaded successfully: " + file.getOriginalFilename();
        } catch (IOException e) {
            return "File upload failed: " + e.getMessage();
        }
    }
}
```

---

### ✅ **Step 3: Testing the File Upload with Postman**

1. Open **Postman**.
2. Set the request type to **POST**.
3. Use the URL: `http://localhost:8080/files/upload`.
4. Select **Body** → **form-data**.
5. Add a **key** with type **File**, and upload your file.

---

### ✅ **Step 4: Customize File Upload Location Using `application.properties`**

You can configure the upload directory in **`application.properties`**.

```properties
file.upload-dir=uploads/
```

Then inject it using **`@Value`**:

```java
import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.bind.annotation.*;
import org.springframework.web.multipart.MultipartFile;

import java.io.File;
import java.io.IOException;

@RestController
@RequestMapping("/files")
public class FileUploadController {

    @Value("${file.upload-dir}")
    private String uploadDir;

    @PostMapping("/upload")
    public String uploadFile(@RequestParam("file") MultipartFile file) {
        try {
            File uploadDirFile = new File(uploadDir);
            if (!uploadDirFile.exists()) {
                uploadDirFile.mkdirs();
            }

            file.transferTo(new File(uploadDir + file.getOriginalFilename()));

            return "File uploaded successfully: " + file.getOriginalFilename();
        } catch (IOException e) {
            return "File upload failed: " + e.getMessage();
        }
    }
}
```

---

# 📥 **2. Handling File Downloads in Spring Boot**

### ✅ **Step 1: Create a Controller to Handle File Downloads**

```java
import org.springframework.core.io.Resource;
import org.springframework.core.io.UrlResource;
import org.springframework.http.HttpHeaders;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.nio.file.Path;
import java.nio.file.Paths;

@RestController
@RequestMapping("/files")
public class FileDownloadController {

    private static final String UPLOAD_DIR = "uploads/";

    @GetMapping("/download/{filename}")
    public ResponseEntity<Resource> downloadFile(@PathVariable String filename) {
        try {
            Path filePath = Paths.get(UPLOAD_DIR).resolve(filename).normalize();
            Resource resource = new UrlResource(filePath.toUri());

            if (resource.exists()) {
                return ResponseEntity.ok()
                        .header(HttpHeaders.CONTENT_DISPOSITION, "attachment; filename=\"" + resource.getFilename() + "\"")
                        .body(resource);
            } else {
                return ResponseEntity.notFound().build();
            }
        } catch (Exception e) {
            return ResponseEntity.internalServerError().build();
        }
    }
}
```

---

### ✅ **Step 2: Testing the File Download with Browser/Postman**

- Open your browser and enter the URL:  
  `http://localhost:8080/files/download/yourfile.txt`

- In **Postman**, set the request type to **GET** and provide the download URL.

---

# 📂 **3. Folder Structure for Uploads and Downloads**

```
project-root/
 └── uploads/
     └── yourfile.txt
```

---

# 🛡 **4. Security Considerations**

1. **Validate File Type**: Restrict uploads to specific file types to prevent malicious file uploads.

   Example:
   ```java
   if (!file.getContentType().equals("text/plain")) {
       throw new IllegalArgumentException("Invalid file type");
   }
   ```

2. **Validate File Name**: Sanitize file names to prevent **path traversal attacks**.

   Example:
   ```java
   String sanitizedFileName = file.getOriginalFilename().replaceAll("[^a-zA-Z0-9.-]", "_");
   ```

3. **Set Proper Permissions**: Ensure that your upload directory has proper **read/write permissions**.

4. **Scan Files**: Use an antivirus scanner to **scan uploaded files** for potential malware.

---

# ✅ **5. Advanced: Upload Multiple Files**

You can also handle **multiple file uploads** using `List<MultipartFile>`.

### Example:

```java
@PostMapping("/upload/multiple")
public String uploadMultipleFiles(@RequestParam("files") List<MultipartFile> files) {
    StringBuilder result = new StringBuilder();

    for (MultipartFile file : files) {
        try {
            file.transferTo(new File(UPLOAD_DIR + file.getOriginalFilename()));
            result.append("Uploaded: ").append(file.getOriginalFilename()).append("\n");
        } catch (IOException e) {
            result.append("Failed: ").append(file.getOriginalFilename()).append(" - ").append(e.getMessage()).append("\n");
        }
    }

    return result.toString();
}
```

---

# 📌 **6. Summary of Endpoints**

| **Endpoint**                 | **Method** | **Description**         |
|------------------------------|------------|-------------------------|
| `/files/upload`              | POST       | Upload a single file    |
| `/files/upload/multiple`     | POST       | Upload multiple files   |
| `/files/download/{filename}` | GET        | Download a file by name |

---
