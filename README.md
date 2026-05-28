<!DOCTYPE html>
<html lang="he">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Animal Trivia</title>

  <script type="module">
    import { initializeApp } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-app.js";
    import {
      getFirestore,
      collection,
      addDoc,
      getDocs,
      query,
      orderBy
    } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-firestore.js";

    const firebaseConfig = {
      apiKey: "AIzaSyCQySWQmVl6BmkTdxAmn7Qn0TNjbJ_gojo",
      authDomain: "animal-trivia-6a9dd.firebaseapp.com",
      projectId: "animal-trivia-6a9dd",
      storageBucket: "animal-trivia-6a9dd.firebasestorage.app",
      messagingSenderId: "326519074510",
      appId: "1:326519074510:web:99d20fcc88799070252a9f"
    };

    const app = initializeApp(firebaseConfig);
    const db = getFirestore(app);

    const questions = [
      {
        q: "איזו חיה היא הגדולה בעולם?",
        options: ["פיל", "כריש", "לווייתן כחול", "ג'ירפה"],
        answer: 2
      },
      {
        q: "כמה לבבות יש לתמנון?",
        options: ["1", "2", "3", "4"],
        answer: 2
      },
      {
        q: "איזו חיה יכולה לישון בעמידה?",
        options: ["סוס", "חתול", "דולפין", "קוף"],
        answer: 0
      }
    ];

    let currentQuestion = 0;
    let score = 0;

    window.startQuiz = () => {
      const name = document.getElementById("name").value;
      if (!name) return alert("כתבו שם");

      window.playerName = name;
      document.getElementById("start-screen").style.display = "none";
      showQuestion();
    };

    function showQuestion() {
      const q = questions[currentQuestion];

      document.getElementById("quiz").innerHTML = `
        <h2>${q.q}</h2>
        ${q.options.map((opt, i) => `
          <button onclick="answerQuestion(${i})">${opt}</button>
        `).join("")}
      `;
    }

    window.answerQuestion = async (index) => {
      if (index === questions[currentQuestion].answer) {
        score += 100;
      }

      currentQuestion++;

      if (currentQuestion >= questions.length) {
        await addDoc(collection(db, "scores"), {
          name: window.playerName,
          score
        });

        showLeaderboard();
        return;
      }

      showQuestion();
    };

    async function showLeaderboard() {
      const q = query(collection(db, "scores"), orderBy("score", "desc"));
      const snapshot = await getDocs(q);

      let html = `<h2>טבלת מובילים</h2>`;

      snapshot.forEach((doc, index) => {
        const data = doc.data();

        html += `
          <p>
            ${index + 1}. ${data.name} - ${data.score}
          </p>
        `;
      });

      document.getElementById("quiz").innerHTML = html;
    }
  </script>

  <style>
    body {
      font-family: Arial;
      direction: rtl;
      text-align: center;
      background: #f5f5f5;
      padding: 30px;
    }

    button {
      display: block;
      margin: 10px auto;
      padding: 15px;
      width: 250px;
      border: none;
      border-radius: 12px;
      background: #4CAF50;
      color: white;
      font-size: 18px;
      cursor: pointer;
    }

    input {
      padding: 12px;
      width: 250px;
      border-radius: 10px;
      border: 1px solid #ccc;
    }
  </style>
</head>

<body>

  <div id="start-screen">
    <h1>🐾 Animal Trivia</h1>

    <input id="name" placeholder="השם שלכם"/>

    <br><br>

    <button onclick="startQuiz()">התחל</button>
  </div>

  <div id="quiz"></div>

</body>
</html>
