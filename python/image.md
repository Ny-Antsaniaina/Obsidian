Voici **le guide complet côté FRONT (React)** pour :

✅ **Uploader une image** (POST)  
✅ **Récupérer toutes les images d’un user** (GET)  
✅ **Savoir si une image appartient à un user**  
➡️ (la base de données te donne l’info via `username` + `name`)

Je te donne **du code React propre, simple et prêt à coller**.

---

# 🟦 1. POST — Upload image côté FRONT (React)

```jsx
import axios from "axios";
import { useState } from "react";

export default function UploadImage({ username }) {
  const [file, setFile] = useState(null);

  const upload = async () => {
    if (!file) return alert("Select a file first!");

    const form = new FormData();
    form.append("image", file);
    form.append("username", username);

    try {
      const res = await axios.post("http://localhost:5000/api/upload", form, {
        headers: { "Content-Type": "multipart/form-data" }
      });

      console.log(res.data);
      alert("Image uploaded!");
    } catch (e) {
      console.error(e);
      alert("Upload failed!");
    }
  };

  return (
    <div>
      <h3>Upload Image</h3>
      <input type="file" onChange={(e) => setFile(e.target.files[0])} />
      <button onClick={upload}>Upload</button>
    </div>
  );
}
```

---

# 🟦 2. GET — Récupérer toutes les images d’un user (React)

```jsx
import axios from "axios";
import { useEffect, useState } from "react";

export default function UserImages({ username }) {
  const [images, setImages] = useState([]);

  const fetchImages = async () => {
    try {
      const res = await axios.get(`http://localhost:5000/api/images/${username}`);
      setImages(res.data.images);
    } catch (e) {
      console.error(e);
    }
  };

  useEffect(() => {
    fetchImages();
  }, [username]);

  return (
    <div>
      <h3>Images of {username}</h3>
      <div style={{ display: "flex", flexWrap: "wrap", gap: "10px" }}>
        {images.map((img) => (
          <div key={img.id}>
            <img
              src={`http://localhost:5000${img.url}`}
              alt={img.name}
              width={150}
              height={150}
              style={{ objectFit: "cover", borderRadius: 8 }}
            />
            <p>{img.name}</p>
          </div>
        ))}
      </div>
    </div>
  );
}
```

---

# ⭐ Très important :

### 👉 Comment savoir si une image appartient à un user ?

Tu n’as **RIEN à faire côté front**.  
L’API te donne déjà l’info :

### Exemple de retour API :

```json
{
  "images": [
    {
      "id": 1,
      "name": "291aa73e88.png",
      "url": "/api/image/john/291aa73e88.png"
    }
  ]
}
```

➡️ L’image appartient à : `john`  
➡️ Le fichier s’appelle : `291aa73e88.png`

**Pourquoi c’est sûr ?**  
Parce que dans ta base tu stockes :

```
username | name
---------|------------------
john     | 291aa73e88.png
```

Donc si tu fais GET `/api/images/john`, tu es 100% sûr que ce sont **les images de John**.

---

# 🟩 Résumé clair

|Tâche|Qui vérifie ?|Détails|
|---|---|---|
|Savoir si une image appartient à un user|**Back-end (DB)**|La table `img(username, name)`|
|Récupérer les images d’un user|Front → GET `/api/images/<username>`|Tu affiches ce que l’API retourne|
|Voir l’image|`<img src="http://localhost:5000/api/image/john/image.png" />`|L’API sert l’image|
|Upload image|POST `/api/upload` avec FormData|Le backend crée dossier + DB|

---

# 🟦 Si tu veux, je te donne :

✅ Le code du **backend Flask pour POST upload**  
➡️ (route complète `/api/upload`)  
OU  
✅ Une version plus sécurisée (JWT)  
OU  
✅ Un tutoriel complet avec React + Flask + PostgreSQL

Dis-moi ce que tu veux !