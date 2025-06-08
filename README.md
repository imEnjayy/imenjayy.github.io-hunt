<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Bonus Hunt Tracker</title>
  <link href="https://cdn.jsdelivr.net/npm/tailwindcss@2.2.19/dist/tailwind.min.css" rel="stylesheet">
  <script src="https://unpkg.com/lucide@latest"></script>
</head>
<body class="bg-gradient-to-br from-black via-gray-900 to-black text-white min-h-screen p-6">
  <div class="max-w-xl mx-auto">
    <h1 class="text-3xl font-bold text-center mb-6">🎰 Bonus Hunt Tracker</h1>
    <div class="flex gap-2 mb-4">
      <input id="slotName" type="text" placeholder="Slot name" class="flex-1 px-3 py-2 rounded bg-gray-800 border border-gray-700" />
      <input id="bonusCost" type="number" placeholder="Bonus cost ($)" class="w-32 px-3 py-2 rounded bg-gray-800 border border-gray-700" />
      <button onclick="addGame()" class="px-4 py-2 bg-green-600 rounded">➕</button>
    </div>
    <div id="gameList" class="space-y-4"></div>
    <div class="mt-6 p-4 rounded-xl bg-gray-900 shadow-lg">
      <div class="text-lg">💰 Total Cost: $<span id="totalCost">0.00</span></div>
      <div class="text-lg">🎉 Total Payout: $<span id="totalPayout">0.00</span></div>
      <div class="text-lg font-bold" id="profitStatus">Profit/Loss: $0.00</div>
      <button onclick="resetHunt()" class="mt-4 w-full bg-red-600 px-4 py-2 rounded">🔁 Reset Hunt</button>
    </div>
  </div>

  <script>
    let games = JSON.parse(localStorage.getItem("bonusHuntGames")) || [];

    function saveGames() {
      localStorage.setItem("bonusHuntGames", JSON.stringify(games));
      renderGames();
    }

    function addGame() {
      const name = document.getElementById("slotName").value.trim();
      const cost = parseFloat(document.getElementById("bonusCost").value);
      if (!name || isNaN(cost)) return;
      games.push({ id: Date.now(), name, cost, collected: false, payout: null });
      document.getElementById("slotName").value = "";
      document.getElementById("bonusCost").value = "";
      saveGames();
    }

    function deleteGame(id) {
      games = games.filter(g => g.id !== id);
      saveGames();
    }

    function updateGame(id, key, value) {
      games = games.map(g => g.id === id ? { ...g, [key]: value } : g);
      saveGames();
    }

    function resetHunt() {
      games = [];
      saveGames();
    }

    function renderGames() {
      const list = document.getElementById("gameList");
      list.innerHTML = "";
      let totalCost = 0;
      let totalPayout = 0;

      games.forEach(game => {
        totalCost += game.cost;
        totalPayout += game.payout || 0;

        const div = document.createElement("div");
        div.className = "bg-gray-800 p-4 rounded";
        div.innerHTML = `
          <div class="flex justify-between items-center mb-2">
            <strong>${game.name}</strong>
            <button onclick="deleteGame(${game.id})" class="text-red-500">🗑️</button>
          </div>
          <div class="text-sm text-gray-300">Cost: $${game.cost.toFixed(2)}</div>
          <div class="flex gap-2 mt-2 items-center">
            <label class="flex items-center gap-1">
              <input type="checkbox" onchange="updateGame(${game.id}, 'collected', this.checked)" ${game.collected ? 'checked' : ''} /> Collected
            </label>
            <input type="number" placeholder="Payout ($)" value="${game.payout ?? ''}" onchange="updateGame(${game.id}, 'payout', parseFloat(this.value) || 0)" class="flex-1 px-2 py-1 bg-gray-700 rounded" />
          </div>
        `;
        list.appendChild(div);
      });

      document.getElementById("totalCost").textContent = totalCost.toFixed(2);
      document.getElementById("totalPayout").textContent = totalPayout.toFixed(2);

      const profit = totalPayout - totalCost;
      const profitEl = document.getElementById("profitStatus");
      profitEl.textContent = `${profit >= 0 ? "Profit" : "Loss"}: $${profit.toFixed(2)}`;
      profitEl.className = `text-lg font-bold ${profit >= 0 ? "text-green-400" : "text-red-500"}`;
    }

    renderGames();
  </script>
</body>
</html>
