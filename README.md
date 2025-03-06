# **📚 Lesson Plan: Building a DBZ Auto Battle Game with Axios**

---

### **🎯 Lesson Goals**

By the end of this lesson, you will:
✅ Understand how to use **Axios** to fetch data from an API  
✅ Learn how to handle **API errors**  
✅ Fetch **multiple characters** and display their data  
✅ Build a **simple auto-battle game** using **randomized attack power**

---

## **🔹 1. Introduction to Axios**

**What is Axios?**

- A **JavaScript library** for making HTTP requests
- Works better than `fetch()` because it:
  - **Automatically converts JSON** (no need for `.json()`)
  - **Handles errors** better
  - **Uses a simpler syntax**

### **✅ Install Axios**

We will use the Axios CDN:

```html
<script src="https://cdn.jsdelivr.net/npm/axios/dist/axios.min.js"></script>
```

If you're using Node.js:

```bash
npm install axios
```

---

## **🔹 2. Fetching a DBZ Character**

The **free DBZ API**:  
🔗 **[API Endpoint](https://dragonball-api.com/api/characters/)**

### **🚀 Fetch Goku’s Data**

```javascript
async function fetchCharacter(id) {
  try {
    let response = await axios.get(
      `https://dragonball-api.com/api/characters/${id}`
    );
    console.log(response.data);
  } catch (error) {
    console.error("Error fetching character:", error);
  }
}
fetchCharacter(1); // Fetch Goku
```

📌 **No need for `.json()`, Axios automatically handles it!**

---

## **🔹 3. Handling Errors in API Requests**

APIs can fail due to:

- **Invalid character ID** (`404 Not Found`).
- **Server issues** (`500 Internal Server Error`).
- **No internet connection**.

### **🚀 Handling Errors Correctly**

```javascript
async function fetchCharacter(id) {
  try {
    let response = await axios.get(
      `https://dragonball-api.com/api/characters/${id}`
    );
    console.log(response.data);
  } catch (error) {
    if (error.response) {
      console.error(`Error ${error.response.status}: Character not found`);
    } else {
      console.error("Network error or no response received");
    }
  }
}
fetchCharacter(999); // Invalid ID for testing error handling
```

📌 **Axios automatically throws errors for failed requests!**

---

## **🔹 4. Fetching Two Characters for Battle**

### **🚀 Fetch Goku and Vegeta for a Battle**

```javascript
async function fetchBattleCharacters() {
  try {
    let [goku, vegeta] = await Promise.all([
      axios.get("https://dragonball-api.com/api/characters/1"),
      axios.get("https://dragonball-api.com/api/characters/2"),
    ]);

    console.log(
      "Goku:",
      goku.data.name,
      "Power Level:",
      goku.data.max_power_level
    );
    console.log(
      "Vegeta:",
      vegeta.data.name,
      "Power Level:",
      vegeta.data.max_power_level
    );
  } catch (error) {
    console.error("Error fetching characters:", error);
  }
}
fetchBattleCharacters();
```

📌 **Fetching multiple characters at the same time using `Promise.all()`!**

---

## **🔹 5. Final Project: Simple DBZ Auto Battle Game**

### **📌 Features**

✅ Select two **random DBZ characters**  
✅ Display **character stats**  
✅ Simulate **an automatic battle** where each character attacks randomly  
✅ Declare a **winner based on the remaining health**

---

### **📜 Full Project Code**

Save this as **`index.html`** and open in a browser.

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>DBZ Auto Battle</title>
    <script src="https://cdn.jsdelivr.net/npm/axios/dist/axios.min.js"></script>
    <style>
      body {
        font-family: Arial, sans-serif;
        text-align: center;
        background-color: #222;
        color: #fff;
      }
      .container {
        max-width: 600px;
        margin: 20px auto;
        padding: 20px;
        background: #333;
        border-radius: 10px;
        box-shadow: 0px 0px 10px rgba(255, 255, 255, 0.2);
      }
      button {
        padding: 10px;
        border: none;
        background: #ffcb05;
        cursor: pointer;
        border-radius: 5px;
        font-size: 16px;
      }
      .battle-area {
        margin-top: 20px;
        padding: 10px;
        background: #444;
        border-radius: 10px;
      }
      img {
        width: 120px;
        border-radius: 5px;
      }
    </style>
  </head>
  <body>
    <div class="container">
      <h2>DBZ Auto Battle</h2>
      <button onclick="startBattle()">Start Battle</button>
      <div id="battleLog" class="battle-area"></div>
    </div>

    <script>
      async function getRandomCharacter() {
        let id = Math.floor(Math.random() * 100) + 1; // Random DBZ character ID
        try {
          let response = await axios.get(
            `https://dragonball-api.com/api/characters/${id}`
          );
          return response.data;
        } catch (error) {
          console.error("Error fetching character:", error);
          return getRandomCharacter(); // Try another if character doesn't exist
        }
      }

      async function startBattle() {
        let battleLog = document.getElementById("battleLog");
        battleLog.innerHTML = "<p>Fetching characters...</p>";

        let [fighter1, fighter2] = await Promise.all([
          getRandomCharacter(),
          getRandomCharacter(),
        ]);

        // Battle starts
        let health1 = fighter1.max_power_level;
        let health2 = fighter2.max_power_level;

        battleLog.innerHTML = `
                <h3>${fighter1.name} VS ${fighter2.name}</h3>
                <p><img src="${fighter1.image}" alt="${fighter1.name}"> VS <img src="${fighter2.image}" alt="${fighter2.name}"></p>
                <p><strong>${fighter1.name}</strong> HP: ${health1}</p>
                <p><strong>${fighter2.name}</strong> HP: ${health2}</p>
                <h3>🔥 Battle Begins! 🔥</h3>
            `;

        // Auto Battle Simulation
        while (health1 > 0 && health2 > 0) {
          let attack1 = Math.floor(Math.random() * 5000);
          let attack2 = Math.floor(Math.random() * 5000);

          health2 -= attack1;
          health1 -= attack2;

          battleLog.innerHTML += `
                    <p>${fighter1.name} attacks for ${attack1} damage!</p>
                    <p>${fighter2.name} attacks for ${attack2} damage!</p>
                `;
        }

        let winner = health1 > 0 ? fighter1.name : fighter2.name;
        battleLog.innerHTML += `<h2>🏆 Winner: ${winner} 🏆</h2>`;
      }
    </script>
  </body>
</html>
```

---

## **🎯 Summary**

✅ Learned **how to use Axios with `async/await`**  
✅ Handled **API errors correctly**  
✅ Fetched **multiple characters** from the DBZ API  
✅ Built a **fun auto-battle game**

🚀 **Next Steps:**

- Add **battle animations**
- Add a **battle history log**
- Allow **user selection of characters**
