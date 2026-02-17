# ✅ **1. The Balises (UI Components) You MUST Know**

### 🧱 **Core Layout Components**

|React Native Component|What it does|
|---|---|
|**View**|Like `<div>` in web. Container for layout.|
|**Text**|Displays text.|
|**Image**|Shows images.|
|**ScrollView**|Scrollable content. Not good for long lists.|
|**SafeAreaView**|Avoids the notch on iOS/Android.|
|**Pressable / TouchableOpacity**|For buttons you can press.|
|**TextInput**|Input fields (email, password, etc.).|

---

### 📱 **Lists**

|Component|Usage|
|---|---|
|**FlatList**|Renders long list efficiently.|
|**SectionList**|List with grouped sections.|

---

### 🎨 **Other Useful Components**

|Component|Usage|
|---|---|
|**Modal**|Popup windows.|
|**ActivityIndicator**|Loading spinner.|
|**StatusBar**|Change status bar color.|

---

# ✅ **2. CSS / Styling You Must Know**

React Native does **not** use classic CSS.  
It uses **JavaScript objects**.

### ⭐ Basic stylesheet

```ts
const styles = StyleSheet.create({
  container: {
    flex: 1,
    padding: 20,
    backgroundColor: "#fff",
  },
});
```

---

## 🎨 **Most important style properties**

### 📏 Layout (Flexbox — very important!)

|Style|Meaning|
|---|---|
|**flex**|Auto expand / shrink.|
|**flexDirection**|'row' or 'column' (default column).|
|**justifyContent**|Align vertically (column) or horizontally (row).|
|**alignItems**|Align opposite axis.|
|**flexWrap**|Allow wrapping.|

🔥 React Native layout = **100% Flexbox**, no CSS grid.

---

### 📐 Size & spacing

|Name|Example|
|---|---|
|width, height|`width: 100`, `height: 50`|
|padding, margin|`padding: 10`, `margin: 20`|
|paddingHorizontal / paddingVertical|`paddingHorizontal: 20`|

---

### 🎨 Colors & Text

|Name|Example|
|---|---|
|color|`color: "white"`|
|backgroundColor|`backgroundColor: "black"`|
|fontSize|`fontSize: 16`|
|fontWeight|`"bold"`|

---

### 🔲 Borders & radius

|Style|Example|
|---|---|
|borderWidth|1|
|borderColor|"#ccc"|
|borderRadius|10|

---

### 💥 Shadows (Android/iOS)

```ts
shadowColor: "#000",
shadowOpacity: 0.2,
shadowOffset: { width: 0, height: 2 },
elevation: 5 // Android
```

---

# ✅ **3. Hooks & Methods You MUST Know**

### 🎣 **React hooks**

|Hook|What it does|
|---|---|
|**useState**|Manage component state.|
|**useEffect**|Run side effects on mount/update.|
|**useRef**|Store mutable value (timer, input ref).|
|**useMemo**|Optimize calculations.|
|**useCallback**|Optimize functions.|

🔥 You will use `useState` + `useEffect` 90% of the time.

---

### 📍 Navigation (React Navigation)

|Hook|Description|
|---|---|
|`useNavigation()`|Navigate between screens|
|`useRoute()`|Access route parameters|

---

### 🌐 API / Network

Use **fetch()** or axios.

```ts
const res = await fetch("https://example.com");
```

---

### 🗂 Local Storage

Using AsyncStorage:

```ts
await AsyncStorage.setItem("token", "123");
```

---

# 🚀 **4. Bonus: React Native utilities you will need**

|Utility|Why|
|---|---|
|**Dimensions**|Get screen width/height|
|**Platform**|Detect iOS/Android|
|**KeyboardAvoidingView**|Avoid keyboard overlap|

---

# 📌 Want a Checklist?

### ✔ Learn these components

View, Text, Image, Pressable, TextInput, ScrollView, FlatList

### ✔ Learn Flexbox (very important)

flex, flexDirection, justifyContent, alignItems

### ✔ Learn essential hooks

useState, useEffect

### ✔ Learn navigation

Stack.Navigator, useNavigation()

### ✔ Learn to fetch API
