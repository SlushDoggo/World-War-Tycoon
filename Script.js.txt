// Player Stats
let player = {
    money: 1000,
    income: 10,
    army: 0,
    navy: 0,
    airForce: 0,
    alliances: 0,
    wars: 0,
    oil: 0,
    factories: 0,
    energy: 100,
    territories: 0,
    techLevel: 1, // Determines research progression
};

// AI Opponents - Updated with more advancement
const aiLevels = [
    { name: "Rebels", army: 20, strength: 5, aggression: 1, techLevel: 1 },
    { name: "Small Nation", army: 50, strength: 10, aggression: 2, techLevel: 2 },
    { name: "Major Power", army: 100, strength: 20, aggression: 4, techLevel: 3 },
    { name: "Superpower", army: 300, strength: 50, aggression: 6, techLevel: 4 }
];

let currentEnemy = aiLevels[0];

// Costs & Upgrades
const unitCosts = { army: 100, navy: 500, airForce: 1000 };
const upgradeCosts = {
    factory: { cost: 500, incomeBoost: 5 },
    oilRefinery: { cost: 1000, oilBoost: 2 },
    powerPlant: { cost: 1500, energyBoost: 20 },
    researchLab: { cost: 2000, techBoost: 1 },
};

// Territory Data (Coordinates)
const territories = {
    "North America": { conquered: false, coords: [50, 50, 150, 150] },
    "Europe": { conquered: false, coords: [200, 100, 300, 200] },
    "Asia": { conquered: false, coords: [350, 250, 450, 350] },
    "Africa": { conquered: false, coords: [500, 100, 600, 200] },
    "South America": { conquered: false, coords: [150, 300, 250, 400] },
    "Oceania": { conquered: false, coords: [600, 250, 700, 350] },
};

// Update the game display
function updateDisplay() {
    document.getElementById("money").innerText = `Money: $${player.money}`;
    document.getElementById("income").innerText = `$${player.income}/sec`;
    document.getElementById("territoryStatus").innerText = `Controlled Territories: ${player.territories}`;
}

// Money generation
function generateMoney() {
    player.money += player.income;
    updateDisplay();
}

// Buy military units
function buyUnit(type) {
    if (player.money >= unitCosts[type] && player.energy >= 1) {
        player.money -= unitCosts[type];
        player[type]++;
        player.energy -= type === 'army' ? 1 : type === 'navy' ? 5 : 10;
        updateDisplay();
    } else {
        alert("Not enough money or energy!");
    }
}

// Buy upgrades
function buyUpgrade(type) {
    if (player.money >= upgradeCosts[type].cost) {
        player.money -= upgradeCosts[type].cost;
        if (type === 'factory') player.income += upgradeCosts[type].incomeBoost;
        if (type === 'oilRefinery') player.oil += upgradeCosts[type].oilBoost;
        if (type === 'powerPlant') player.energy += upgradeCosts[type].energyBoost;
        if (type === 'researchLab') player.techLevel += upgradeCosts[type].techBoost;
        updateDisplay();
    } else {
        alert("Not enough money!");
    }
}

// Attack Enemy (territory conquest)
function attackEnemy(region) {
    if (!territories[region].conquered) {
        territories[region].conquered = true;
        player.territories++;
        levelUpAI();  // Increase AI difficulty as territories are conquered
        updateDisplay();
        updateMap(); // Update the map when a territory is conquered
    }
}

// Update the map
function updateMap() {
    for (let region in territories) {
        let territory = territories[region];
        let area = document.querySelector(`area[alt="${region}"]`);
        
        if (territory.conquered) {
            area.style.fill = 'green';  // Mark conquered territory
        } else {
            area.style.fill = 'transparent'; // Default
        }
    }
}

// Increase AI level as player advances
function levelUpAI() {
    const conqueredTerritories = player.territories;
    
    if (conqueredTerritories > 5 && currentEnemy.techLevel < 2) {
        currentEnemy.techLevel = 2;
        currentEnemy.strength = 10;
        currentEnemy.aggression = 3;
    } else if (conqueredTerritories > 10 && currentEnemy.techLevel < 3) {
        currentEnemy.techLevel = 3;
        currentEnemy.strength = 20;
        currentEnemy.aggression = 5;
    } else if (conqueredTerritories > 20 && currentEnemy.techLevel < 4) {
        currentEnemy.techLevel = 4;
        currentEnemy.strength = 30;
        currentEnemy.aggression = 7;
    }

    console.log(`AI Level: ${currentEnemy.techLevel}, Strength: ${currentEnemy.strength}, Aggression: ${currentEnemy.aggression}`);
}

// Start the game
function startGame() {
    setInterval(generateMoney, 1000);
    updateDisplay();
}
