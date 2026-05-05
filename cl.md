Looking at your code, I'll help you implement the `getImg()` method and a route to fetch lists with image URLs.

## Issues I noticed first

**Bug in `listRepository.getList()`** — the SQL is malformed:

```javascript
// ❌ Wrong
const sql = "select * from username = $1 and role = $2"

// ✅ Fixed
const sql = "SELECT * FROM lists WHERE username = $1"
```

---

## 1. `ImgService` — implement `getImg()`

```javascript
const path = require('path')
const fs = require('fs')

class ImgService {
    saveImage() { /* ...existing code... */ }

    getImg(filename) {
        const filePath = path.resolve('./uploads', filename)
        if (!fs.existsSync(filePath)) {
            return null
        }
        return filePath
    }
}
```

---

## 2. `ListRepository` — fix `getList()`

```javascript
async getList(username) {
    const con = await this.database.getConnection();
    const sql = "SELECT * FROM lists WHERE username = $1"
    try {
        const res = await con.query(sql, [username]);
        return res.rows          // ← return rows, not the raw result
    } catch (error) {
        console.log('error to get lists');
        throw new Error(error)
    }
}
```

---

## 3. `ImgController` — add two routes

```javascript
const express = require('express')
const router = express.Router()
const jwt = require('jsonwebtoken')
const path = require('path')
const { Database } = require('../database/db')
const { GenerateToken } = require('../token/generateToken')
const { ListRepository } = require('../repository/listRepository')
const { ListService } = require('../service/listService')
const { ImgService } = require('../service/imgService')
const { AdminRepository } = require('../repository/adminRepository')

class ImgController {
    constructor() {
        const imgService = new ImgService()
        const upload = imgService.saveImage()

        // ─── POST /admin/create ───────────────────────────────────────
        router.post('/admin/create', upload.single('image'), async (req, res) => {
            try {
                const { username, name, description, price, quantity } = req.body

                if (!req.file) {
                    return res.status(400).json({ error: 'Image requise' })
                }

                const database = new Database()
                const generateToken = new GenerateToken(jwt)
                const listRepository = new ListRepository(database)
                const adminRepository = new AdminRepository(database)
                const listService = new ListService(adminRepository, listRepository, generateToken)

                const result = await listService.createList(
                    req, username,
                    req.file.path, req.file.filename,
                    name, description, price, quantity
                )

                res.status(201).json({
                    message: "create list successfully",
                    data: result
                })
            } catch (error) {
                console.log('error to create list', error)
                res.status(500).json({ error: 'Internal server error' })
            }
        })

        // ─── GET /admin/lists?username=xxx ────────────────────────────
        // Returns all lists with a ready-to-use imageUrl field
        router.get('/admin/lists', async (req, res) => {
            try {
                const { username } = req.query

                if (!username) {
                    return res.status(400).json({ error: 'username is required' })
                }

                const database = new Database()
                const generateToken = new GenerateToken(jwt)
                const listRepository = new ListRepository(database)
                const adminRepository = new AdminRepository(database)
                const listService = new ListService(adminRepository, listRepository, generateToken)

                const result = await listService.getAllList(req, username)

                // Attach imageUrl to each list item
                const baseUrl = `${req.protocol}://${req.get('host')}`
                const listsWithImages = result.lists.map(item => ({
                    ...item,
                    imageUrl: `${baseUrl}/admin/image/${item.filename}`
                }))

                res.status(200).json({
                    lists: listsWithImages,
                    token: result.token
                })
            } catch (error) {
                console.log('error to get lists', error)
                res.status(500).json({ error: 'Internal server error' })
            }
        })

        // ─── GET /admin/image/:filename ───────────────────────────────
        // Streams the actual image file
        router.get('/admin/image/:filename', (req, res) => {
            const { filename } = req.params
            const filePath = imgService.getImg(filename)

            if (!filePath) {
                return res.status(404).json({ error: 'Image not found' })
            }

            res.sendFile(filePath)
        })
    }

    getRouter() {
        return router
    }
}

module.exports = { ImgController }
```

---

## 4. Register static uploads + router in `app.js`

```javascript
const express = require('express')
const app = express()

app.use(express.json())

// Serve uploaded files as static (alternative direct access)
app.use('/uploads', express.static('./uploads'))

// Your controller router
const { ImgController } = require('./controller/imgController')
app.use('/', new ImgController().getRouter())
```

---

## How it works end-to-end

```
GET /admin/lists?username=john
```

```json
{
  "lists": [
    {
      "id": 1,
      "username": "john",
      "name": "Product A",
      "description": "...",
      "price": 10,
      "quantity": 5,
      "filename": "1714000000abc.jpg",
      "imageUrl": "http://localhost:3000/admin/image/1714000000abc.jpg"
    }
  ]
}
```

```
GET /admin/image/1714000000abc.jpg  → streams the image binary
```

The client fetches the list JSON first, then uses each `imageUrl` to display the image — no extra work needed on the frontend.