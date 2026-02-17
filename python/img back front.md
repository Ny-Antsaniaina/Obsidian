Here is a **clean, complete, working guide** for **image CRUD in Flask + PostgreSQL + React**.  
Your current code has several issues (e.g. `send_from_directory` incorrectly used, missing update/delete, incorrect select-one).  
I’ll give you:

### ✅ Correct Flask backend

### ✅ Full CRUD (create, list all, get one, update, delete)

### ✅ How to serve images correctly

### ✅ React code to upload and fetch images

---

# ✅ 1. FLASK – CORRECT IMAGE CRUD API

## ✔ Project structure

```
backend/
 ├─ app.py
 ├─ controllers/
 │    └─ img_controller.py
 ├─ repository/
 │    └─ img_repository.py
 ├─ service/
 │    └─ img_service.py
 ├─ uploads/
 │     └─ images/
```

---

# ✅ img_service.py — save image + delete image

```python
import os
import uuid

UPLOAD_FOLDER = "./uploads/images"
os.makedirs(UPLOAD_FOLDER, exist_ok=True)

def save_image(file):
    ext = os.path.splitext(file.filename)[1]
    file_id = uuid.uuid4().hex + ext

    file_path = os.path.join(UPLOAD_FOLDER, file_id)
    file.save(file_path)

    return file_id, f"/uploads/images/{file_id}"

def delete_image(path):
    file_path = "." + path
    if os.path.exists(file_path):
        os.remove(file_path)
```

---

# ✅ img_repository.py — database CRUD

```python
from database.db import get_database

def create_img(name, path):
    conn = get_database('photo')
    cursor = conn.cursor()
    cursor.execute(
        "INSERT INTO img (name, path_name) VALUES (%s, %s) RETURNING id",
        (name, path)
    )
    img_id = cursor.fetchone()[0]
    conn.commit()
    return img_id

def get_all_img():
    conn = get_database('photo')
    cursor = conn.cursor()
    cursor.execute("SELECT * FROM img ORDER BY id DESC")
    rows = cursor.fetchall()

    return [
        {"id": row[0], "name": row[1], "path_name": row[2], "created_at": row[3]}
        for row in rows
    ]

def get_one_img(img_id):
    conn = get_database('photo')
    cursor = conn.cursor()
    cursor.execute("SELECT * FROM img WHERE id=%s", (img_id,))
    row = cursor.fetchone()
    if not row: return None
    return {"id": row[0], "name": row[1], "path_name": row[2], "created_at": row[3]}

def update_img(img_id, name, path=None):
    conn = get_database('photo')
    cursor = conn.cursor()

    if path:
        cursor.execute(
            "UPDATE img SET name=%s, path_name=%s WHERE id=%s",
            (name, path, img_id)
        )
    else:
        cursor.execute(
            "UPDATE img SET name=%s WHERE id=%s",
            (name, img_id)
        )
    conn.commit()

def delete_img(img_id):
    conn = get_database('photo')
    cursor = conn.cursor()
    cursor.execute("DELETE FROM img WHERE id=%s", (img_id,))
    conn.commit()
```

---

# ✅ img_controller.py — FULL CRUD ROUTES

```python
from flask import Blueprint, request, jsonify, send_from_directory
from service.img_service import save_image, delete_image
from repository.img_repository import (
    create_img, get_all_img, get_one_img, update_img, delete_img
)

img = Blueprint("img", __name__)

# ---------------------------
# CREATE
# ---------------------------
@img.route("/api/img", methods=["POST"])
def upload_image():
    file = request.files.get("image")
    name = request.form.get("name")

    file_id, path = save_image(file)
    img_id = create_img(name, path)

    return jsonify({"message": "image created", "id": img_id, "path": path})


# ---------------------------
# SELECT ALL
# ---------------------------
@img.route("/api/images", methods=["GET"])
def all_images():
    return jsonify(get_all_img())


# ---------------------------
# SELECT ONE
# ---------------------------
@img.route("/api/img/<int:img_id>", methods=["GET"])
def get_image(img_id):
    data = get_one_img(img_id)
    if not data:
        return jsonify({"error": "not found"}), 404
    return jsonify(data)


# ---------------------------
# UPDATE
# ---------------------------
@img.route("/api/img/<int:img_id>", methods=["PUT"])
def update_image_route(img_id):
    name = request.form.get("name")
    file = request.files.get("image")

    if file:
        # delete old img
        old = get_one_img(img_id)
        if old:
            delete_image(old["path_name"])

        new_id, new_path = save_image(file)
        update_img(img_id, name, new_path)
    else:
        update_img(img_id, name)

    return jsonify({"message": "updated"})


# ---------------------------
# DELETE
# ---------------------------
@img.route("/api/img/<int:img_id>", methods=["DELETE"])
def delete_image_route(img_id):
    old = get_one_img(img_id)
    if old:
        delete_image(old["path_name"])

    delete_img(img_id)
    return jsonify({"message": "deleted"})


# ---------------------------
# SERVE IMAGES
# ---------------------------
@img.route("/uploads/images/<path:filename>")
def serve_image(filename):
    return send_from_directory("./uploads/images", filename)
```

---

# ✅ app.py (register blueprint)

```python
from flask import Flask
from controllers.img_controller import img

app = Flask(__name__)
app.register_blueprint(img)

if __name__ == "__main__":
    app.run(debug=True)
```

---

# 🎯 2. React — HOW TO FETCH AND DISPLAY IMAGES

## ✔ Upload Image (React)

```jsx
function Upload() {
  const [file, setFile] = useState(null);
  const [name, setName] = useState("");

  const uploadImg = async () => {
    const formData = new FormData();
    formData.append("image", file);
    formData.append("name", name);

    await fetch("http://localhost:5000/api/img", {
      method: "POST",
      body: formData
    });

    alert("uploaded!");
  };

  return (
    <div>
      <input type="text" onChange={e => setName(e.target.value)} placeholder="Name" />
      <input type="file" onChange={e => setFile(e.target.files[0])} />
      <button onClick={uploadImg}>Upload</button>
    </div>
  );
}
```

---

## ✔ Fetch ALL images

```jsx
import { useEffect, useState } from "react";

function Gallery() {
  const [photos, setPhotos] = useState([]);

  useEffect(() => {
    fetch("http://localhost:5000/api/images")
      .then(res => res.json())
      .then(data => setPhotos(data));
  }, []);

  return (
    <div>
      {photos.map(p => (
        <div key={p.id}>
          <p>{p.name}</p>
          <img
            src={`http://localhost:5000${p.path_name}`}
            width="200"
          />
        </div>
      ))}
    </div>
  );
}

export default Gallery;
```

---
