## Lian porfotlio

https://zumh.github.io/

## Add image inside static/uploads/
- edit data/carousel.yaml file
- https://hugocodex.org/add-ons/slider-carousel/

## Possible dynamic image loading solution

Yes, you can dynamically load images from a folder (e.g., `uploads`) in Hugo without manually editing the `yaml` file every time. Hugo provides powerful features like **Page Resources** and **Hugo Pipes** that allow you to work with files dynamically. Here's how you can achieve this:

---

### **Steps to Dynamically Load Images from a Folder**

#### 1. **Organize Your Folder Structure**
Place all your images in a specific folder, e.g., `static/uploads/`. Hugo's `static` folder is ideal for files that don't need processing (like images).

```
your-hugo-project/
├── static/
│   └── uploads/
│       ├── image1.jpg
│       ├── image2.jpg
│       └── image3.jpg
├── content/
├── layouts/
└── config.yaml
```

---

#### 2. **Use Hugo's `readDir` Function to List Files**
Hugo has a built-in function called `readDir` that allows you to read files from a directory dynamically. You can use this to list all images in the `uploads` folder.

---

#### 3. **Create a Partial or Template to Load Images**
In your `layouts/` directory, create a partial or template to dynamically load the images. For example:

```html
<!-- layouts/partials/carousel.html -->
<div class="carousel">
  {{ $uploads := "uploads" }} <!-- Path to the uploads folder -->
  {{ range readDir (printf "static/%s" $uploads) }}
    {{ if in .Name ".jpg" ".jpeg" ".png" ".gif" }} <!-- Filter for image files -->
      <div class="carousel-item">
        <img src="/{{ $uploads }}/{{ .Name }}" alt="{{ .Name }}">
      </div>
    {{ end }}
  {{ end }}
</div>
```

---

#### 4. **Include the Partial in Your Page**
Include the partial in your page template (e.g., `layouts/_default/single.html`):

```html
{{ partial "carousel.html" . }}
```

---

#### 5. **Optional: Add Metadata or Content for Each Image**
If you want to associate metadata (like `content_html`) with each image dynamically, you can use a data file (e.g., `data/images.yaml`) or front matter.

##### Example with `data/images.yaml`:
```yaml
# data/images.yaml
images:
  - image: /uploads/image1.jpg
    content_html: "1"
  - image: /uploads/image2.jpg
    content_html: "2"
```

Then, update your partial to use this data:

```html
<!-- layouts/partials/carousel.html -->
<div class="carousel">
  {{ range .Site.Data.images }}
    <div class="carousel-item">
      <img src="{{ .image }}" alt="{{ .content_html }}">
      <div class="content">{{ .content_html | safeHTML }}</div>
    </div>
  {{ end }}
</div>
```

---

#### 6. **Automate with a Script (Optional)**
If you want to fully automate the process (e.g., generate the `data/images.yaml` file whenever new images are added), you can write a small script (e.g., in Python or Bash) to scan the `uploads` folder and update the `yaml` file.

---

### **Advantages of This Approach**
- **Dynamic**: No need to manually update the `yaml` file every time you add or remove images.
- **Flexible**: You can easily filter, sort, or process images using Hugo's templating functions.
- **Scalable**: Works well for large numbers of images.

---

### **Alternative: Use Page Bundles**
If you want to associate images with specific pages or posts, consider using **Page Bundles** in Hugo. This allows you to store images alongside your content files and access them dynamically.

For example:
```
content/
└── posts/
    └── my-post/
        ├── index.md
        ├── image1.jpg
        └── image2.jpg
```

You can then access these images in your template using `.Resources`:

```html
{{ range .Resources.ByType "image" }}
  <img src="{{ .RelPermalink }}" alt="{{ .Name }}">
{{ end }}
```

---

By using these methods, you can dynamically load images from a folder in Hugo without manually editing the `yaml` file every time.