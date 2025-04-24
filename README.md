# Shajare-<!DOCTYPE html><html lang="fa">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>فرم اطلاعات و شجره‌نامه</title>
  <script src="https://balkangraph.com/js/familytree.js"></script>
  <link href="https://fonts.googleapis.com/css2?family=Vazir:wght@300;400;500;700&display=swap" rel="stylesheet">
  <style>
    body {
      font-family: 'Vazir', sans-serif;
      direction: rtl;
      background: linear-gradient(to bottom, #eef2f3, #ffffff);
      padding: 20px;
      margin: 0;
      animation: fadeIn 2s ease-out;
    }
    @keyframes fadeIn {
      from { opacity: 0; }
      to { opacity: 1; }
    }
    .container {
      width: 100%;
      max-width: 1200px;
      margin: auto;
    }
    .form-section {
      background: #ffffff;
      padding: 30px;
      border-radius: 20px;
      box-shadow: 0 6px 16px rgba(0,0,0,0.15);
      margin-bottom: 30px;
      animation: slideIn 1s ease-out forwards;
    }
    @keyframes slideIn {
      from { transform: translateY(30px); opacity: 0; }
      to { transform: translateY(0); opacity: 1; }
    }
    h2 {
      font-size: 24px;
      margin-bottom: 20px;
      color: #333;
      border-bottom: 2px solid #007bff;
      padding-bottom: 10px;
    }
    label {
      display: block;
      margin-top: 15px;
      font-weight: bold;
    }
    input, select, button {
      width: 100%;
      padding: 12px;
      margin-top: 5px;
      border-radius: 8px;
      border: 1px solid #ccc;
      font-size: 16px;
    }
    button {
      margin-top: 20px;
      background-color: #007bff;
      color: #fff;
      font-weight: bold;
      cursor: pointer;
      transition: 0.3s;
    }
    button:hover {
      background-color: #0056b3;
    }
    .tree-container {
      background: #ffffff;
      border-radius: 20px;
      padding: 30px;
      box-shadow: 0 6px 16px rgba(0,0,0,0.15);
      margin-top: 30px;
    }
    #tree {
      width: 100%;
      height: 600px;
    }
  </style>
</head>
<body>
  <div class="container">
    <div class="form-section">
      <h2>فرم اطلاعات شخصی</h2>
      <label>نام و نام خانوادگی:</label>
      <input type="text" id="name">
      <label>کد ملی:</label>
      <input type="text" id="nationalCode">
      <label>محل سکونت:</label>
      <input type="text" id="location">
      <label>مدرک تحصیلی:</label>
      <input type="text" id="education">
      <label>شغل (در صورت شاغل بودن):</label>
      <input type="text" id="job">
      <label>شماره تلفن:</label>
      <input type="text" id="phone">
    </div>
    <div class="form-section">
      <h2>افزودن اعضای شجره‌نامه (تا ۷ نسل)</h2>
      <div id="members"></div>
      <button onclick="addMember()">افزودن عضو</button>
      <button onclick="drawTree()">نمایش نمودار شجره‌نامه</button>
      <button onclick="saveChanges()">ایجاد تغییرات</button>
      <button onclick="finalizeForm()">ثبت نهایی</button>
      <select id="layoutMode" onchange="drawTree()">
        <option value="tree">نمودار درختی</option>
        <option value="radial">نمودار دایره‌ای</option>
      </select>
    </div>
    <div class="tree-container">
      <div id="tree"></div>
    </div>
  </div>  <script>
    let members = [];
    function addMember() {
      const container = document.createElement('div');
      container.innerHTML = `
        <hr>
        <label>نام عضو:</label>
        <input type="text" class="member-name">
        <label>جنسیت:</label>
        <select class="member-gender">
          <option value="male">مرد</option>
          <option value="female">زن</option>
        </select>
        <label>شغل:</label>
        <input type="text" class="member-job">
        <label>محل سکونت:</label>
        <input type="text" class="member-location">
        <label>پدر (ID درختی):</label>
        <input type="text" class="member-father">
        <label>مادر (ID درختی):</label>
        <input type="text" class="member-mother">
        <label>شماره تلفن:</label>
        <input type="text" class="member-phone">
      `;
      document.getElementById("members").appendChild(container);
    }

    function drawTree() {
      members = [];
      const names = document.querySelectorAll('.member-name');
      const genders = document.querySelectorAll('.member-gender');
      const jobs = document.querySelectorAll('.member-job');
      const locations = document.querySelectorAll('.member-location');
      const fathers = document.querySelectorAll('.member-father');
      const mothers = document.querySelectorAll('.member-mother');
      const phones = document.querySelectorAll('.member-phone');

      const nameSet = new Set();
      for (let i = 0; i < names.length; i++) {
        const name = names[i].value.trim();
        if (!name || nameSet.has(name)) continue;
        nameSet.add(name);
        members.push({
          id: i + 1,
          name,
          gender: genders[i].value,
          job: jobs[i].value,
          location: locations[i].value,
          father: fathers[i].value ? parseInt(fathers[i].value) : null,
          mother: mothers[i].value ? parseInt(mothers[i].value) : null,
          phone: phones[i].value
        });
      }

      const treeData = members.map(member => ({
        id: member.id,
        pid: member.father || undefined,
        mid: member.mother || undefined,
        name: member.name,
        gender: member.gender === 'male' ? 'M' : 'F',
        job: member.job,
        location: member.location,
        phone: member.phone,
        tags: [member.gender]
      }));

      FamilyTree.templates.male = Object.assign({}, FamilyTree.templates.base);
      FamilyTree.templates.male.node = '<rect x="0" y="0" width="200" height="100" rx="10" ry="10" fill="#add8e6" stroke="#007bff" stroke-width="1"></rect>';
      FamilyTree.templates.female = Object.assign({}, FamilyTree.templates.base);
      FamilyTree.templates.female.node = '<rect x="0" y="0" width="200" height="100" rx="10" ry="10" fill="#f9c2d4" stroke="#e83e8c" stroke-width="1"></rect>';

      new FamilyTree(document.getElementById("tree"), {
        nodes: treeData,
        nodeBinding: {
          field_0: "name",
          field_1: "job",
          field_2: "location",
          field_3: "phone"
        },
        tags: {
          male: {
            template: "male"
          },
          female: {
            template: "female"
          }
        },
        layout: document.getElementById("layoutMode").value,
        enableZoom: true,
        animation: {
          type: "expand",
          duration: 800
        },
        scaleInitial: FamilyTree.match.boundary,
        orientation: FamilyTree.orientation.vertical
      });
    }

    function saveChanges() {
      alert("تغییرات با موفقیت ذخیره شد.");
    }

    function finalizeForm() {
      alert("اطلاعات ثبت نهایی شد.");
    }
  </script></body>
</html>
