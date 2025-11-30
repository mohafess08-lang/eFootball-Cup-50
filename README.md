# eFootball-Cup-50<!DOCTYPE html>
<html lang="ar">
<head>
  <meta charset="UTF-8">
  <title>نظام الفرق والنقاط</title>
  <style>
    body { font-family: Arial, sans-serif; }
    h2 { margin-top: 20px; }
    ul { list-style-type: none; padding: 0; }
    li { margin: 5px 0; }
    button { margin-left: 10px; }
  </style>
</head>
<body>

<h1>نظام الفرق والنقاط</h1>
<div id="groupsContainer"></div>

<script>
let teams = JSON.parse(localStorage.getItem('teams')) || [
  // المجموعة 1
  { name: "Elhady – AC Milan", points: 0, group: "المجموعة 1" },
  { name: "Hamza – Borussia Dortmund", points: 0, group: "المجموعة 1" },
  { name: "Fouad – Hollande", points: 0, group: "المجموعة 1" },
  { name: "Touati – Japan", points: 0, group: "المجموعة 1" },

  // المجموعة 2
  { name: "Soufiane – Barcelona", points: 0, group: "المجموعة 2" },
  { name: "Oussama – Manchester City", points: 0, group: "المجموعة 2" },
  { name: "Mehdi – Morocco", points: 0, group: "المجموعة 2" },
  { name: "Ben Taleb – Liverpool", points: 0, group: "المجموعة 2" },

  // المجموعة 3
  { name: "Karim – Bayern Munich", points: 0, group: "المجموعة 3" },
  { name: "Ayoub – Arsenal", points: 0, group: "المجموعة 3" },
  { name: "Houssam – Algeria", points: 0, group: "المجموعة 3" },
  { name: "Samir – Paris Saint-Germain", points: 0, group: "المجموعة 3" },

  // المجموعة 4
  { name: "Aizen – Inter Milan", points: 0, group: "المجموعة 4" },
  { name: "Said – Real Madrid", points: 0, group: "المجموعة 4" },
  { name: "Mohmed – Manchester United", points: 0, group: "المجموعة 4" },
  { name: "Dovix – Algeria", points: 0, group: "المجموعة 4" }
];

function saveTeams() {
  localStorage.setItem('teams', JSON.stringify(teams));
}

function renderTeams() {
  const container = document.getElementById('groupsContainer');
  container.innerHTML = '';

  // الحصول على كل المجموعات الفريدة
  const groups = [...new Set(teams.map(t => t.group))];

  groups.forEach(group => {
    const groupHeader = document.createElement('h2');
    groupHeader.textContent = group;
    container.appendChild(groupHeader);

    const ul = document.createElement('ul');

    teams.filter(t => t.group === group).forEach(team => {
      const li = document.createElement('li');
      li.textContent = `${team.name} - ${team.points} نقاط`;

      const button = document.createElement('button');
      button.textContent = 'فاز';
      button.onclick = () => {
        team.points += 3;
        renderTeams();
        saveTeams();
      };

      li.appendChild(button);
      ul.appendChild(li);
    });

    container.appendChild(ul);
  });
}

renderTeams();
</script>

</body>
</html>
