<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>TCG Game</title>
    <link href="https://cdn.jsdelivr.net/npm/tailwindcss@2.2.19/dist/tailwind.min.css" rel="stylesheet">
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Inter', sans-serif;
            background-color: #1a202c; /* Dark background */
            color: #e2e8f0; /* Light text */
            display: flex;
            flex-direction: column;
            min-height: 100vh;
        }

        /* Utility classes for better responsiveness and card display */
        .card-grid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(140px, 1fr));
            gap: 1rem;
        }

        .card {
            background-color: #2d3748; /* Darker blue-gray */
            border-radius: 0.75rem;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
            overflow: hidden;
            text-align: center;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: space-between;
            padding: 0.75rem;
            position: relative;
            cursor: pointer;
            transition: transform 0.2s ease-in-out, box-shadow 0.2s ease-in-out;
        }

        .card:hover {
            transform: translateY(-5px);
            box-shadow: 0 8px 12px rgba(0, 0, 0, 0.2);
        }

        .card img {
            width: 100%;
            height: auto;
            border-radius: 0.5rem;
            margin-bottom: 0.5rem;
            object-fit: cover;
            max-width: 100px;
            max-height: 140px;
        }

        .card h4 {
            font-weight: 600;
            font-size: 1em;
            margin-bottom: 0.25rem;
            color: #f7fafc;
        }

        .card button {
            background-color: #4a5568; /* Slightly lighter gray */
            color: #fff;
            padding: 0.3rem 0.6rem;
            border-radius: 0.5rem;
            font-size: 0.8em;
            cursor: pointer;
            transition: background-color 0.2s;
            border: none;
            margin-top: 0.5rem;
        }

        .card button:hover:enabled {
            background-color: #636b77;
        }

        .card button:disabled {
            background-color: #4a556880;
            cursor: not-allowed;
        }

        /* Card background colors based on type/rarity */
        .card-bg-green { background-color: #38a169; } /* Emerald */
        .card-bg-gray { background-color: #a0aec0; } /* Slate */
        .card-bg-red { background-color: #e53e3e; } /* Crimson */
        .card-bg-blue { background-color: #4299e1; } /* Sapphire */
        .card-bg-brown { background-color: #9c6c4c; } /* Earthy */
        .card-bg-gold { background-color: #d69e2e; } /* Gold */
        .card-bg-purple { background-color: #805ad5; } /* Amethyst */
        .card-bg-black { background-color: #2d3748; } /* Shadow */
        .card-bg-yellow { background-color: #ecc94b; } /* Sunburst */

        /* Deck List Styling */
        .deck-list-container {
            background-color: #2d3748;
            border-radius: 0.75rem;
            padding: 1rem;
            height: fit-content;
            overflow-y: auto;
        }

        .deck-list ul {
            list-style: none;
            padding: 0;
        }

        .deck-list li {
            display: flex;
            align-items: center;
            margin-bottom: 0.5rem;
            padding: 0.25rem 0;
            border-bottom: 1px solid #4a5568;
        }

        .deck-list li:last-child {
            border-bottom: none;
        }

        .deck-list li button {
            background-color: #e53e3e;
            color: white;
            border: none;
            border-radius: 0.25rem;
            padding: 0.1em 0.5em;
            margin-left: 0.5rem;
            cursor: pointer;
            transition: background-color 0.2s;
        }

        .deck-list li button:hover {
            background-color: #c53030;
        }

        /* Modals */
        .modal {
            display: none;
            position: fixed;
            z-index: 1000;
            left: 0;
            top: 0;
            width: 100%;
            height: 100%;
            overflow: auto;
            background-color: rgba(0, 0, 0, 0.7);
            backdrop-filter: blur(5px);
            justify-content: center;
            align-items: center;
        }

        .modal-content {
            background-color: #2d3748;
            margin: auto;
            padding: 20px;
            border-radius: 12px;
            position: relative;
            width: 90%;
            max-width: 600px;
            box-shadow: 0 8px 30px rgba(0, 0, 0, 0.4);
            display: flex;
            flex-direction: column;
            gap: 1rem;
        }

        .close-button {
            color: #aaa;
            position: absolute;
            top: 10px;
            right: 15px;
            font-size: 28px;
            font-weight: bold;
            cursor: pointer;
            transition: color 0.2s;
        }

        .close-button:hover,
        .close-button:focus {
            color: #fff;
            text-decoration: none;
            cursor: pointer;
        }

        .modal-buttons button {
            background-color: #4299e1;
            color: white;
            padding: 0.75rem 1.5rem;
            border-radius: 0.5rem;
            cursor: pointer;
            transition: background-color 0.2s;
            border: none;
            margin: 0.5rem;
        }

        .modal-buttons button:hover {
            background-color: #3182ce;
        }

        #void-card-list, #deck-search-content {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(120px, 1fr));
            gap: 1rem;
            padding: 1rem;
            max-height: 400px;
            overflow-y: auto;
            border: 1px solid #4a5568;
            border-radius: 0.5rem;
        }

        /* Battlefield specific styling */
        #battlefield-container {
            flex-grow: 1;
            display: none; /* Hidden by default */
            flex-direction: column;
            gap: 1rem;
            padding: 1rem;
            width: 100%;
        }

        .game-zone {
            display: flex;
            border: 2px dashed #4a5568;
            border-radius: 0.75rem;
            padding: 1rem;
            min-height: 120px;
            align-items: center;
            gap: 0.5rem;
            overflow-x: auto;
            background-color: #2d3748;
        }

        .drag-over {
            border-color: #4299e1;
            background-color: #4a5568;
        }

        .hp-badge {
            position: absolute;
            bottom: 8px; /* Adjust position as needed */
            right: 8px;
            background-color: rgba(0, 0, 0, 0.7);
            color: white;
            font-size: 0.75em;
            padding: 2px 6px;
            border-radius: 8px;
        }

        .hp-bar-wrap {
            position: absolute;
            top: 5px;
            left: 50%;
            transform: translateX(-50%);
            width: 80%;
            height: 6px;
            background-color: #555;
            border-radius: 3px;
            overflow: hidden;
        }

        .hp-bar {
            height: 100%;
            transition: width 0.3s ease-in-out, background-color 0.3s ease-in-out;
        }

        .card.on-field {
            min-width: 80px;
            min-height: 110px;
            padding: 0.25rem;
            position: relative;
        }
        .card.on-field img {
            width: 80px;
            height: 110px;
            margin-bottom: 0;
        }

        .deck-zone, .void-zone {
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            min-width: 80px;
            min-height: 110px;
            padding: 0.5rem;
            border: 2px solid #636b77;
            border-radius: 0.75rem;
            background-color: #2d3748;
            cursor: pointer;
            transition: background-color 0.2s;
        }
        .deck-zone:hover, .void-zone:hover {
            background-color: #4a5568;
        }
        .deck-zone img, .void-zone img {
            width: 60px;
            height: 80px;
            object-fit: cover;
            border-radius: 0.25rem;
            margin-bottom: 0.25rem;
        }

        /* Hand card menu */
        #hand-card-menu, #deck-actions-menu {
            position: absolute;
            background-color: #333;
            border: 1px solid #555;
            border-radius: 8px;
            padding: 8px;
            box-shadow: 0px 4px 10px rgba(0,0,0,0.3);
            z-index: 100;
            display: none;
            flex-direction: column;
            gap: 4px;
        }

        #hand-card-menu button, #deck-actions-menu button {
            background-color: #4a5568;
            color: #fff;
            padding: 6px 12px;
            border-radius: 6px;
            cursor: pointer;
            transition: background-color 0.2s;
            border: none;
            width: 100%;
            text-align: left;
        }

        #hand-card-menu button:hover, #deck-actions-menu button:hover {
            background-color: #636b77;
        }

        /* Loading Overlay */
        #loading-overlay {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(0, 0, 0, 0.8);
            display: flex;
            justify-content: center;
            align-items: center;
            z-index: 10000;
            flex-direction: column;
            gap: 1rem;
            color: white;
            font-size: 1.5rem;
        }
    </style>
</head>
<body class="p-4">

    <!-- Loading Overlay -->
    <div id="loading-overlay" class="hidden">
        <div class="animate-spin rounded-full h-16 w-16 border-t-2 border-b-2 border-white"></div>
        <p>Loading...</p>
    </div>

    <!-- Main Content Area -->
    <main class="flex flex-col lg:flex-row flex-grow gap-4">

        <!-- Builder Page -->
        <section id="builder-page" class="flex flex-col lg:flex-row w-full gap-4">
            <!-- Deck Selector and Actions -->
            <div id="deck-slot-selector" class="bg-gray-800 p-4 rounded-xl shadow-lg lg:w-1/4 w-full">
                <h2 class="text-xl font-bold mb-4 text-white">Deck Builder</h2>
                <div class="flex items-center space-x-2 mb-4">
                    <label for="deck-slot-select" class="text-gray-300">Current Deck:</label>
                    <select id="deck-slot-select" class="flex-grow bg-gray-700 text-white border border-gray-600 rounded-md p-2">
                        <!-- Options populated by JS -->
                    </select>
                </div>
                <div class="flex flex-wrap gap-2 mb-4">
                    <button id="add-deck-slot-btn" class="flex-grow bg-blue-600 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded-lg transition duration-200">
                        Add New Deck
                    </button>
                    <button id="rename-deck-slot-btn" class="flex-grow bg-yellow-600 hover:bg-yellow-700 text-white font-bold py-2 px-4 rounded-lg transition duration-200">
                        Rename
                    </button>
                    <button id="delete-deck-slot-btn" class="flex-grow bg-red-600 hover:bg-red-700 text-white font-bold py-2 px-4 rounded-lg transition duration-200">
                        Delete
                    </button>
                </div>
                <h3 id="deck-title" class="text-lg font-semibold text-white mb-4">Selected Deck</h3>

                <!-- Deck List -->
                <div class="deck-list-container flex flex-col justify-between items-center text-gray-200 p-4 rounded-lg h-96 overflow-y-auto">
                    <ul id="deck-list" class="deck-list w-full">
                        <!-- Deck cards will be listed here -->
                    </ul>
                    <p class="mt-4 text-center">Total Cards: <span id="card-count" class="font-bold">0</span>/50</p>
                </div>
            </div>

            <!-- Card Gallery and Filters -->
            <div id="gallery-container" class="bg-gray-800 p-4 rounded-xl shadow-lg flex-grow w-full">
                <h2 class="text-xl font-bold mb-4 text-white">Card Gallery</h2>
                <!-- Filters -->
                <div id="filters" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4 mb-6">
                    <input type="text" id="filter-name" placeholder="Filter by Name" class="bg-gray-700 text-white border border-gray-600 rounded-md p-2">
                    <select id="filter-category" class="bg-gray-700 text-white border border-gray-600 rounded-md p-2">
                        <option value="">All Categories</option>
                        <option value="creature">Creature</option>
                        <option value="artifact">Artifact</option>
                        <option value="spell">Spell</option>
                        <option value="domain">Domain</option>
                    </select>
                    <select id="filter-color" class="bg-gray-700 text-white border border-gray-600 rounded-md p-2">
                        <option value="">All Colors</option>
                        <option value="green">Green</option>
                        <option value="gray">Gray</option>
                        <option value="red">Red</option>
                        <option value="blue">Blue</option>
                        <option value="brown">Brown</option>
                        <option value="purple">Purple</option>
                        <option value="black">Black</option>
                        <option value="yellow">Yellow</option>
                    </select>
                    <select id="filter-type" class="bg-gray-700 text-white border border-gray-600 rounded-md p-2">
                        <option value="">All Types</option>
                        <option value="creature">Creature</option>
                        <option value="spell">Spell</option>
                        <option value="domain">Domain</option>
                        <option value="artifact">Artifact</option>
                    </select>
                    <select id="filter-rarity" class="bg-gray-700 text-white border border-gray-600 rounded-md p-2">
                        <option value="">All Rarities</option>
                        <option value="common">Common</option>
                        <option value="rare">Rare</option>
                        <option value="legendary">Legendary</option>
                    </select>
                    <input type="text" id="filter-archetype" placeholder="Filter by Archetype" class="bg-gray-700 text-white border border-gray-600 rounded-md p-2">
                    <input type="text" id="filter-ability" placeholder="Filter by Ability" class="bg-gray-700 text-white border border-gray-600 rounded-md p-2">
                </div>

                <!-- Card Gallery -->
                <div id="card-gallery" class="card-grid">
                    <!-- Cards will be rendered here by JS -->
                </div>
            </div>
        </section>

        <!-- Gameplay Page -->
        <section id="battlefield-container" class="flex flex-col w-full">
            <div id="battlefield" class="flex flex-col flex-grow gap-4 p-4 bg-gray-800 rounded-xl shadow-lg">
                <div class="flex justify-between items-center bg-gray-700 p-3 rounded-lg mb-4 shadow">
                    <button id="back-to-builder-btn" class="bg-gray-600 hover:bg-gray-700 text-white font-bold py-2 px-4 rounded-lg transition duration-200">
                        Back to Builder
                    </button>
                    <div class="text-white text-lg font-semibold flex-grow text-center">
                        <span id="phase-player" class="capitalize"></span>'s Turn - Phase: <span id="phase-name" class="capitalize"></span>
                    </div>
                    <button id="next-phase-btn" class="bg-green-600 hover:bg-green-700 text-white font-bold py-2 px-4 rounded-lg transition duration-200">
                        Next Phase
                    </button>
                </div>

                <!-- Opponent Zones -->
                <div class="flex flex-col gap-2">
                    <h3 class="text-lg font-semibold text-white">Opponent's Hand (<span id="opponent-hand-count">0</span>)</h3>
                    <div id="opponent-hand" class="game-zone min-h-[140px] flex-wrap">
                        <!-- Opponent's hand cards -->
                    </div>
                    <h3 class="text-lg font-semibold text-white">Opponent's Creatures</h3>
                    <div id="opponent-creatures-zone" class="game-zone">
                        <!-- Opponent's creatures -->
                    </div>
                    <h3 class="text-lg font-semibold text-white">Opponent's Domains</h3>
                    <div id="opponent-domains-zone" class="game-zone">
                        <!-- Opponent's domains -->
                    </div>
                </div>

                <!-- Player Zones -->
                <div class="flex flex-col gap-2 mt-auto">
                    <h3 class="text-lg font-semibold text-white">Your Domains</h3>
                    <div id="player-domains-zone" class="game-zone">
                        <!-- Player's domains -->
                    </div>
                    <h3 class="text-lg font-semibold text-white">Your Creatures</h3>
                    <div id="player-creatures-zone" class="game-zone">
                        <!-- Player's creatures -->
                    </div>
                    <h3 class="text-lg font-semibold text-white">Your Hand (<span id="player-hand-count">0</span>)</h3>
                    <div id="player-hand" class="game-zone min-h-[140px] flex-wrap">
                        <!-- Player's hand cards -->
                    </div>
                </div>
            </div>
        </section>
    </main>

    <!-- Floating Buttons for Builder Page -->
    <div class="fixed bottom-4 right-4 flex space-x-2 z-50" id="builder-floating-buttons">
        <button id="toggle-deck-btn" class="bg-purple-600 hover:bg-purple-700 text-white font-bold py-3 px-6 rounded-full shadow-lg transition duration-200">
            Toggle Deck List
        </button>
        <button id="start-game-btn" class="bg-green-600 hover:bg-green-700 text-white font-bold py-3 px-6 rounded-full shadow-lg transition duration-200">
            Start Game
        </button>
    </div>

    <!-- Image Modal (for full card view) -->
    <div id="image-modal" class="modal">
        <div id="modal-img-content" class="modal-content relative p-4 flex flex-col items-center">
            <span class="close-button" id="close-modal-img">&times;</span>
            <img id="modal-img" class="max-w-xs rounded-xl shadow-lg mb-4" src="" alt="Full Card Image">
            <h2 class="text-xl font-bold text-white mb-2"></h2>
            <div class="text-gray-300 text-sm mb-2"></div>
            <div class="text-gray-400 text-xs text-center"></div>
        </div>
    </div>

    <!-- Input Modal (replaces prompt()) -->
    <div id="input-modal" class="modal">
        <div class="modal-content">
            <h3 id="input-modal-title" class="text-xl font-bold text-white mb-4"></h3>
            <p id="input-modal-message" class="text-gray-300 mb-4"></p>
            <input type="text" id="input-modal-field" class="bg-gray-700 text-white border border-gray-600 rounded-md p-2 w-full mb-4">
            <div class="modal-buttons flex justify-end">
                <button id="input-modal-cancel" class="bg-gray-600 hover:bg-gray-700">Cancel</button>
                <button id="input-modal-confirm">Confirm</button>
            </div>
        </div>
    </div>

    <!-- Confirm Modal (replaces confirm()) -->
    <div id="confirm-modal" class="modal">
        <div class="modal-content">
            <h3 id="confirm-modal-title" class="text-xl font-bold text-white mb-4"></h3>
            <p id="confirm-modal-message" class="text-gray-300 mb-4"></p>
            <div class="modal-buttons flex justify-end">
                <button id="confirm-modal-cancel" class="bg-gray-600 hover:bg-gray-700">Cancel</button>
                <button id="confirm-modal-ok">OK</button>
            </div>
        </div>
    </div>

    <!-- Deck Search Modal -->
    <div id="deck-search-modal" class="modal">
        <div class="modal-content">
            <span class="close-button" id="close-deck-search">&times;</span>
            <h3 class="text-xl font-bold text-white mb-4">Search Your Deck</h3>
            <div id="deck-search-content" class="card-grid">
                <!-- Cards from deck will be displayed here -->
            </div>
        </div>
    </div>

    <!-- Void Modal -->
    <div id="void-modal" class="modal">
        <div class="modal-content">
            <span class="close-button" id="close-void-modal">&times;</span>
            <h3 class="text-xl font-bold text-white mb-4">Cards in Void</h3>
            <div id="void-card-list" class="card-grid">
                <!-- Cards from void will be displayed here -->
            </div>
        </div>
    </div>

    <!-- Hand Card Action Menu -->
    <div id="hand-card-menu" class="bg-gray-700 text-white rounded-lg p-2 shadow-xl border border-gray-600">
        <button id="play-creature-btn" class="block w-full text-left px-3 py-2 hover:bg-gray-600 rounded-md">Play Creature</button>
        <button id="play-domain-btn" class="block w-full text-left px-3 py-2 hover:bg-gray-600 rounded-md">Play Domain</button>
        <button id="send-to-void-hand-btn" class="block w-full text-left px-3 py-2 hover:bg-gray-600 rounded-md">Send to Void</button>
    </div>

    <!-- Deck Actions Menu -->
    <div id="deck-actions-menu" class="bg-gray-700 text-white rounded-lg p-2 shadow-xl border border-gray-600">
        <button id="draw-card-btn" class="block w-full text-left px-3 py-2 hover:bg-gray-600 rounded-md">Draw Card</button>
        <button id="search-deck-btn" class="block w-full text-left px-3 py-2 hover:bg-gray-600 rounded-md">Search Deck</button>
        <button id="shuffle-deck-btn" class="block w-full text-left px-3 py-2 hover:bg-gray-600 rounded-md">Shuffle Deck</button>
    </div>

    <!-- Field Card Action Menu -->
    <div id="field-card-menu" class="bg-gray-700 text-white rounded-lg p-2 shadow-xl border border-gray-600">
        <button id="rotate-card-btn" class="block w-full text-left px-3 py-2 hover:bg-gray-600 rounded-md">Rotate Card</button>
        <button id="add-hp-btn" class="block w-full text-left px-3 py-2 hover:bg-gray-600 rounded-md">Add HP</button>
        <button id="remove-hp-btn" class="block w-full text-left px-3 py-2 hover:bg-gray-600 rounded-md">Remove HP</button>
        <button id="send-to-void-field-btn" class="block w-full text-left px-3 py-2 hover:bg-gray-600 rounded-md">Send to Void</button>
    </div>

    <!-- Firebase SDK Imports -->
    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
        import { getAuth, signInAnonymously, signInWithCustomToken, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
        import { getFirestore, doc, getDoc, setDoc, onSnapshot, collection, query, deleteDoc } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";

        // ==========================
        // === DUMMY CARD DATA ===
        // ==========================
        // This array simulates your card database. In a real application, this would
        // likely be loaded from a backend or a larger data file.
        const dummyCards = [
            {
                id: "card001", name: "Forest Sprite", image: "https://placehold.co/80x110/ADD8E6/000000?text=Forest+Sprite",
                hp: 10, atk: 2, def: 1, cost: 1, rarity: "Common", type: "Creature", category: "Creature", color: "Green",
                text: "A nimble sprite that thrives in lush forests."
            },
            {
                id: "card002", name: "Stone Golem", image: "https://placehold.co/80x110/B0C4DE/000000?text=Stone+Golem",
                hp: 15, atk: 3, def: 3, cost: 3, rarity: "Rare", type: "Creature", category: "Creature", color: "Gray",
                text: "A formidable guardian carved from solid rock."
            },
            {
                id: "card003", name: "Fire Blast", image: "https://placehold.co/80x110/FF7F50/000000?text=Fire+Blast",
                cost: 2, rarity: "Common", type: "Spell", category: "Spell", color: "Red",
                text: "Deal 5 damage to a target creature."
            },
            {
                id: "card004", name: "Healing Touch", image: "https://placehold.co/80x110/90EE90/000000?text=Healing+Touch",
                cost: 1, rarity: "Common", type: "Spell", category: "Spell", color: "Green",
                text: "Restore 7 HP to a target creature."
            },
            {
                id: "card005", name: "Mountain Domain", image: "https://placehold.co/80x110/D2B48C/000000?text=Mountain+Domain",
                rarity: "Common", type: "Domain", category: "Domain", color: "Brown",
                text: "Provides 1 Red mana per turn."
            },
            {
                id: "card006", name: "River Domain", image: "https://placehold.co/80x110/ADD8E6/000000?text=River+Domain",
                rarity: "Common", type: "Domain", category: "Domain", color: "Blue",
                text: "Provides 1 Blue mana per turn."
            },
            {
                id: "card007", name: "Ancient Dragon", image: "https://placehold.co/80x110/DC143C/000000?text=Ancient+Dragon",
                hp: 25, atk: 10, def: 8, cost: 7, rarity: "Legendary", type: "Creature", category: "Creature", color: "Red",
                text: "A mythical beast with unparalleled power. Only one allowed per deck."
            },
            {
                id: "card008", name: "Mystic Orb", image: "https://placehold.co/80x110/BA55D3/000000?text=Mystic+Orb",
                cost: 3, rarity: "Rare", type: "Artifact", category: "Artifact", color: "Purple",
                text: "Once per turn, draw a card."
            },
            {
                id: "card009", name: "Shadow Assassin", image: "https://placehold.co/80x110/4F4F4F/FFFFFF?text=Shadow+Assassin",
                hp: 8, atk: 5, def: 2, cost: 4, rarity: "Rare", type: "Creature", category: "Creature", color: "Black",
                text: "Can bypass defender creatures."
            },
            {
                id: "card010", name: "Divine Shield", image: "https://placehold.co/80x110/FFD700/000000?text=Divine+Shield",
                cost: 3, rarity: "Common", type: "Spell", category: "Spell", color: "Yellow",
                text: "Grant a creature +3 DEF until end of turn."
            }
        ];

        // ==========================
        // === FIRESTORE VARIABLES ===
        // ==========================
        let app;
        let db;
        let auth;
        let userId = null;
        let isAuthReady = false;

        // Ensure these global variables are defined by the Canvas environment
        const appId = typeof __app_id !== 'undefined' ? __app_id : 'default-app-id';
        const firebaseConfig = typeof __firebase_config !== 'undefined' ? JSON.parse(__firebase_config) : {};
        const initialAuthToken = typeof __initial_auth_token !== 'undefined' ? __initial_auth_token : null;

        // ==========================
        // === CONSTANTS & STATE ===
        // ==========================
        const PHASES = [
            { turn: 'player', phase: 'draw' },
            { turn: 'player', phase: 'main' },
            { turn: 'player', phase: 'end' },
            { turn: 'opponent', phase: 'draw' },
            { turn: 'opponent', phase: 'main' },
            { turn: 'opponent', phase: 'end' }
        ];

        let gameState = {
            playerDeck: [],
            playerHand: [],
            playerCreatures: [],
            playerDomains: [],
            playerVoid: [],
            opponentDeck: [],
            opponentHand: [],
            opponentCreatures: [],
            opponentDomains: [],
            opponentVoid: [],
            turn: "player",
            phase: "draw",
            phaseIndex: 0 // Added to track current phase for cycling
        };

        // --- DECK DATA (Now managed by Firestore) --- //
        // These will be updated by Firestore listeners
        let deckSlots = ["Deck 1"]; // Default initial slot
        let decks = { "Deck 1": {} }; // Default initial empty deck
        let currentDeckSlot = "Deck 1"; // Default current slot

        // ==========================
        // === DOM REFERENCES ===
        // ==========================
        const loadingOverlay = document.getElementById('loading-overlay');

        const deckSlotSelect = document.getElementById('deck-slot-select');
        const addDeckSlotBtn = document.getElementById('add-deck-slot-btn');
        const renameDeckSlotBtn = document.getElementById('rename-deck-slot-btn');
        const deleteDeckSlotBtn = document.getElementById('delete-deck-slot-btn');
        const deckTitle = document.getElementById('deck-title');
        const startGameBtn = document.getElementById('start-game-btn');
        const backToBuilderBtn = document.getElementById('back-to-builder-btn');
        const battlefieldContainer = document.getElementById('battlefield-container');
        const builderPage = document.getElementById('builder-page');
        const gallery = document.getElementById('card-gallery');
        const deckList = document.getElementById('deck-list');
        const cardCount = document.getElementById('card-count');
        const toggleDeckBtn = document.getElementById('toggle-deck-btn');
        const deckPanel = document.getElementById('deck-slot-selector'); // Renamed to refer to the whole panel

        // Phase display
        const phasePlayerSpan = document.getElementById('phase-player');
        const phaseNameSpan = document.getElementById('phase-name');
        const nextPhaseBtn = document.getElementById('next-phase-btn');

        // Modals
        const imageModal = document.getElementById('image-modal');
        const imageModalImg = document.getElementById('modal-img');
        const imageModalContent = document.getElementById('modal-img-content');
        const closeImageModalBtn = document.getElementById('close-modal-img');

        const inputModal = document.getElementById('input-modal');
        const inputModalTitle = document.getElementById('input-modal-title');
        const inputModalMessage = document.getElementById('input-modal-message');
        const inputModalField = document.getElementById('input-modal-field');
        const inputModalCancelBtn = document.getElementById('input-modal-cancel');
        const inputModalConfirmBtn = document.getElementById('input-modal-confirm');

        const confirmModal = document.getElementById('confirm-modal');
        const confirmModalTitle = document.getElementById('confirm-modal-title');
        const confirmModalMessage = document.getElementById('confirm-modal-message');
        const confirmModalCancelBtn = document.getElementById('confirm-modal-cancel');
        const confirmModalOkBtn = document.getElementById('confirm-modal-ok');

        const deckSearchModal = document.getElementById('deck-search-modal');
        const closeDeckSearchModalBtn = document.getElementById('close-deck-search');
        const deckSearchContent = document.getElementById('deck-search-content');

        const voidModal = document.getElementById('void-modal');
        const closeVoidModalBtn = document.getElementById('close-void-modal');
        const voidCardList = document.getElementById('void-card-list');

        // Gameplay Menus
        const handCardMenu = document.getElementById('hand-card-menu');
        const playCreatureBtn = document.getElementById('play-creature-btn');
        const playDomainBtn = document.getElementById('play-domain-btn');
        const sendToVoidHandBtn = document.getElementById('send-to-void-hand-btn');

        const deckActionsMenu = document.getElementById('deck-actions-menu');
        const drawCardBtn = document.getElementById('draw-card-btn');
        const searchDeckBtn = document.getElementById('search-deck-btn');
        const shuffleDeckBtn = document.getElementById('shuffle-deck-btn');

        const fieldCardMenu = document.getElementById('field-card-menu');
        const rotateCardBtn = document.getElementById('rotate-card-btn');
        const addHpBtn = document.getElementById('add-hp-btn');
        const removeHpBtn = document.getElementById('remove-hp-btn');
        const sendToVoidFieldBtn = document.getElementById('send-to-void-field-btn');

        // ==========================
        // === FIRESTORE SETUP & AUTH ===
        // ==========================

        /**
         * Initializes Firebase and sets up authentication.
         * Sets up Firestore listeners for deck data.
         */
        async function initializeFirebase() {
            showLoadingOverlay("Initializing game...");
            try {
                // Initialize Firebase app
                app = initializeApp(firebaseConfig);
                db = getFirestore(app);
                auth = getAuth(app);

                // Sign in the user. Use custom token if available, otherwise anonymously.
                if (initialAuthToken) {
                    await signInWithCustomToken(auth, initialAuthToken);
                } else {
                    await signInAnonymously(auth);
                }

                // Listen for auth state changes
                onAuthStateChanged(auth, (user) => {
                    if (user) {
                        userId = user.uid;
                        isAuthReady = true;
                        console.log("Firebase authenticated. User ID:", userId);
                        // Once authenticated, set up Firestore listeners for deck data
                        setupFirestoreListeners();
                        hideLoadingOverlay();
                    } else {
                        userId = null;
                        isAuthReady = false;
                        console.log("Firebase not authenticated.");
                        showLoadingOverlay("Authentication failed. Please refresh.");
                    }
                });

            } catch (error) {
                console.error("Error initializing Firebase:", error);
                showLoadingOverlay("Failed to load game. Error: " + error.message);
            }
        }

        /**
         * Sets up real-time listeners for deck data from Firestore.
         */
        function setupFirestoreListeners() {
            if (!db || !userId) {
                console.warn("Firestore or User ID not available for listeners.");
                return;
            }

            // Listener for deck slots
            const deckSlotsDocRef = doc(db, `artifacts/${appId}/users/${userId}/deckData/deckSlots`);
            onSnapshot(deckSlotsDocRef, (docSnap) => {
                if (docSnap.exists()) {
                    const data = docSnap.data();
                    deckSlots = data.slots || ["Deck 1"];
                    console.log("Deck slots updated:", deckSlots);
                    // Ensure currentDeckSlot is valid after update
                    if (!deckSlots.includes(currentDeckSlot)) {
                        currentDeckSlot = deckSlots[0] || "Deck 1";
                    }
                    saveCurrentDeckSlotToFirestore(currentDeckSlot); // Save current slot to firestore
                    refreshDeckSlotSelect();
                    updateDeckDisplay(); // Refresh deck display based on new slots
                } else {
                    console.log("No deck slots found. Initializing default.");
                    deckSlots = ["Deck 1"];
                    saveDeckState(); // Save default if not found
                    refreshDeckSlotSelect();
                    updateDeckDisplay();
                }
            }, (error) => {
                console.error("Error listening to deck slots:", error);
            });

            // Listener for the current deck
            // This listener will be dynamically updated when currentDeckSlot changes
            // We need a separate mechanism for this or rely on updateDeckDisplay to fetch.
            // For simplicity, we'll let `updateDeckDisplay` fetch the current deck from the `decks` object.
            // The `decks` object will be populated by a separate listener on the `decks` collection.

            // Listener for all decks in the collection
            const decksCollectionRef = collection(db, `artifacts/${appId}/users/${userId}/decks`);
            onSnapshot(decksCollectionRef, (snapshot) => {
                const newDecks = {};
                snapshot.forEach(doc => {
                    newDecks[doc.id] = JSON.parse(doc.data().deckData); // Parse the stored JSON string
                });
                decks = newDecks;
                console.log("All decks updated:", decks);
                updateDeckDisplay(); // Refresh UI after all decks are loaded/updated
            }, (error) => {
                console.error("Error listening to decks collection:", error);
            });
        }


        // ==========================
        // === DECK MANAGEMENT (Firestore) ===
        // ==========================

        /**
         * Saves the entire deck state (deck slots and all decks) to Firestore.
         * This function should be called after any modification to `deckSlots` or `decks`.
         */
        async function saveDeckState() {
            if (!userId) {
                console.error("User not authenticated. Cannot save deck state.");
                return;
            }
            showLoadingOverlay("Saving deck...");
            try {
                // Save deckSlots array
                const deckSlotsDocRef = doc(db, `artifacts/${appId}/users/${userId}/deckData/deckSlots`);
                await setDoc(deckSlotsDocRef, { slots: deckSlots });

                // Save each individual deck
                for (const slotName in decks) {
                    const deckDocRef = doc(db, `artifacts/${appId}/users/${userId}/decks/${slotName}`);
                    // Stringify the deck object as Firestore prefers flat structures or stringified JSON for complex objects
                    await setDoc(deckDocRef, { deckData: JSON.stringify(decks[slotName]) });
                }
                console.log("Deck state saved to Firestore.");
            } catch (error) {
                console.error("Error saving deck state:", error);
            } finally {
                hideLoadingOverlay();
            }
        }

        /**
         * Saves the current deck slot selection to Firestore.
         */
        async function saveCurrentDeckSlotToFirestore(slot) {
            if (!userId) return;
            try {
                const currentSlotDocRef = doc(db, `artifacts/${appId}/users/${userId}/deckData/currentDeckSlot`);
                await setDoc(currentSlotDocRef, { currentSlot: slot });
                console.log("Current deck slot saved:", slot);
            } catch (error) {
                console.error("Error saving current deck slot:", error);
            }
        }

        /**
         * Loads the deck state from Firestore.
         * The actual loading happens via `onSnapshot` listeners set up in `setupFirestoreListeners`.
         * This function mainly serves to initiate the load and set initial state if no data exists.
         */
        async function loadDeckState() {
            if (!userId) {
                console.warn("User not authenticated. Cannot load deck state.");
                return;
            }
            showLoadingOverlay("Loading decks...");
            try {
                const deckSlotsDocRef = doc(db, `artifacts/${appId}/users/${userId}/deckData/deckSlots`);
                const docSnap = await getDoc(deckSlotsDocRef);

                if (!docSnap.exists()) {
                    console.log("No existing deck slots found in Firestore. Setting up default.");
                    deckSlots = ["Deck 1"];
                    decks = { "Deck 1": {} };
                    currentDeckSlot = "Deck 1";
                    await saveDeckState(); // Save the default state
                } else {
                    // `onSnapshot` will handle updates, just ensure currentDeckSlot is loaded
                    const currentSlotDocRef = doc(db, `artifacts/${appId}/users/${userId}/deckData/currentDeckSlot`);
                    const currentSlotSnap = await getDoc(currentSlotDocRef);
                    if (currentSlotSnap.exists()) {
                        const savedSlot = currentSlotSnap.data().currentSlot;
                        if (deckSlots.includes(savedSlot)) {
                            currentDeckSlot = savedSlot;
                        } else {
                            currentDeckSlot = deckSlots[0] || "Deck 1"; // Fallback if saved slot is no longer valid
                        }
                    } else {
                        currentDeckSlot = deckSlots[0] || "Deck 1";
                    }
                    console.log("Deck state initiated from Firestore.");
                }
            } catch (error) {
                console.error("Error loading deck state:", error);
            } finally {
                hideLoadingOverlay();
            }
        }

        /**
         * Returns the current deck object.
         */
        function getCurrentDeck() {
            return decks[currentDeckSlot] || {};
        }

        /**
         * Sets the current deck object and triggers a save to Firestore.
         * @param {object} deckObj - The updated deck object.
         */
        function setCurrentDeck(deckObj) {
            decks[currentDeckSlot] = deckObj;
            saveDeckState(); // Now saves to Firestore
        }

        /**
         * Refreshes the deck slot dropdown and updates the displayed deck title.
         */
        function refreshDeckSlotSelect() {
            deckSlotSelect.innerHTML = "";
            deckSlots.forEach(slot => {
                const opt = document.createElement('option');
                opt.value = slot;
                opt.textContent = slot;
                deckSlotSelect.appendChild(opt);
            });
            deckSlotSelect.value = currentDeckSlot;
            deckTitle.textContent = currentDeckSlot;
        }

        // ==========================
        // === RENDERING / UI ===
        // ==========================

        /**
         * Shows the battlefield view and hides the builder view.
         */
        function showBattlefield() {
            builderPage.style.display = 'none';
            battlefieldContainer.style.display = 'flex';
            document.getElementById('builder-floating-buttons').style.display = 'none';
        }

        /**
         * Shows the builder view and hides the battlefield view.
         */
        function showBuilder() {
            builderPage.style.display = 'flex';
            battlefieldContainer.style.display = 'none';
            document.getElementById('builder-floating-buttons').style.display = 'flex';
        }

        /**
         * Determines the CSS background class for a card based on its color(s).
         * @param {object} card - The card object.
         * @returns {string} The CSS class name.
         */
        function getCardBgClass(card) {
            let colors = Array.isArray(card.color) ? card.color : [card.color];
            colors = colors.filter(Boolean).map(c => c.toLowerCase());
            if (colors.length === 1) return `card-bg-${colors[0]}`;
            if (colors.length === 2) return `card-bg-${colors[0]}-${colors[1]}`; // For dual-color cards
            return `card-bg-gold`; // Default for colorless or multi-color
        }

        /**
         * Updates the display of the current turn and phase.
         */
        function updatePhaseBar() {
            phasePlayerSpan.textContent = gameState.turn;
            phaseNameSpan.textContent = gameState.phase;
        }

        /**
         * Retrieves the array representing a game zone from the gameState.
         * @param {string} zoneId - The ID of the zone.
         * @returns {Array|null} The array of cards in the zone, or null if not found.
         */
        function getZoneArray(zoneId) {
            switch (zoneId) {
                case "player-creatures-zone": return gameState.playerCreatures;
                case "player-domains-zone": return gameState.playerDomains;
                case "player-void-zone": return gameState.playerVoid;
                case "opponent-creatures-zone": return gameState.opponentCreatures;
                case "opponent-domains-zone": return gameState.opponentDomains;
                case "opponent-void-zone": return gameState.opponentVoid;
                default: return null;
            }
        }

        /**
         * Updates the display of the current deck in the builder.
         */
        function updateDeckDisplay() {
            const deck = getCurrentDeck();
            deckList.innerHTML = '';
            let total = 0;

            // Group cards by category
            const sections = {
                creature: [],
                artifact: [],
                spell: [],
                domain: []
            };

            for (const [id, count] of Object.entries(deck)) {
                const card = dummyCards.find(c => c.id === id);
                if (!card) continue;
                const cat = getCardCategory(card);
                if (sections.hasOwnProperty(cat)) {
                    sections[cat].push({ card, count });
                }
                total += count;
            }

            // Section display order
            const sectionNames = [
                { key: "creature", label: "Creatures" },
                { key: "artifact", label: "Artifacts" },
                { key: "spell", label: "Spells" },
                { key: "domain", label: "Domains" }
            ];

            for (const { key, label } of sectionNames) {
                if (sections[key].length === 0) continue;

                // Add section heading
                const heading = document.createElement('li');
                heading.className = 'font-bold mt-3 mb-1 text-blue-300';
                heading.textContent = label;
                deckList.appendChild(heading);

                for (const { card, count } of sections[key]) {
                    const li = document.createElement('li');
                    li.className = 'flex items-center justify-between py-1 px-2 hover:bg-gray-700 rounded-md transition duration-150';

                    const cardInfo = document.createElement('div');
                    cardInfo.className = 'flex items-center flex-grow';

                    const img = document.createElement('img');
                    img.src = card.image;
                    img.alt = card.name;
                    img.className = 'w-10 h-auto mr-2 rounded-md shadow-sm';
                    img.onerror = function() {
                        this.onerror = null;
                        this.src = "https://placehold.co/80x110/333/FFF?text=Error"; // Placeholder for broken images
                    };

                    const span = document.createElement('span');
                    span.textContent = `${card.name} ×${count} `;
                    span.className = 'text-gray-200';

                    cardInfo.appendChild(img);
                    cardInfo.appendChild(span);
                    li.appendChild(cardInfo);

                    const removeBtn = document.createElement('button');
                    removeBtn.textContent = '−';
                    removeBtn.className = 'bg-red-500 hover:bg-red-600 text-white font-bold p-1 rounded-full w-6 h-6 flex items-center justify-center text-sm';
                    removeBtn.onclick = () => {
                        let current = getCurrentDeck(); // Get latest
                        current[card.id]--;
                        if (current[card.id] <= 0) {
                            delete current[card.id];
                        }
                        setCurrentDeck(current); // This will save to Firestore
                        // updateDeckDisplay() is called by onSnapshot for decks
                        renderGallery();
                        // setTimeout(() => deckPanel.classList.add('show'), 0); // Not needed with Firestore
                    };
                    li.appendChild(removeBtn);
                    deckList.appendChild(li);
                }
            }

            cardCount.textContent = total;
            // setCurrentDeck(deck); // Already handled by the listener
            // saveDeckState(); // Already handled by setCurrentDeck which is called by the listener
        }

        /**
         * Creates a div element for a card in the gallery.
         * @param {object} card - The card object.
         * @returns {HTMLElement} The created card div.
         */
        function createCardDiv(card) {
            const deck = getCurrentDeck();
            const div = document.createElement('div');
            div.className = 'card';
            if (card.rarity) {
                div.setAttribute('data-rarity', card.rarity);
            }
            div.classList.add(getCardBgClass(card));

            const img = document.createElement('img');
            img.src = card.image;
            img.onerror = function() {
                this.onerror = null;
                this.src = "https://placehold.co/80x110/333/FFF?text=Error"; // Placeholder for broken images
            };
            img.alt = card.name;
            img.onclick = (e) => {
                e.stopPropagation();
                showFullCardModal(card); // Pass the raw card object
            };
            div.appendChild(img);

            const name = document.createElement('h4');
            name.textContent = card.name;
            div.appendChild(name);

            const stats = document.createElement('div');
            stats.className = 'text-gray-400 text-xs mb-1';
            stats.innerHTML = [
                card.hp !== undefined ? `HP: ${card.hp}` : '',
                card.atk !== undefined ? `ATK: ${card.atk}` : '',
                card.def !== undefined ? `DEF: ${card.def}` : '',
                card.cost !== undefined ? `Cost: ${card.cost}` : ''
            ].filter(Boolean).join(' | ');
            if (stats.innerHTML.trim() !== '') div.appendChild(stats);

            const details = document.createElement('div');
            details.className = 'text-gray-500 text-xs mb-2';
            details.textContent = [
                card.rarity,
                Array.isArray(card.type) ? card.type.join(', ') : card.type
            ].filter(Boolean).join(' | ');
            if (details.textContent.trim() !== '') div.appendChild(details);

            const btn = document.createElement('button');
            btn.textContent = "Add to Deck";
            btn.disabled = !canAddCard(card);
            btn.onclick = () => {
                if (!canAddCard(card)) return;
                let current = getCurrentDeck(); // Get latest
                current[card.id] = (current[card.id] || 0) + 1;
                setCurrentDeck(current); // This will save to Firestore
                // updateDeckDisplay() is called by onSnapshot for decks
                renderGallery(); // Re-render gallery to update button states
            };
            div.appendChild(btn);
            return div;
        }

        /**
         * Returns the category of a card (e.g., 'creature', 'spell').
         * @param {object} card - The card object.
         * @returns {string} The card's category in lowercase.
         */
        function getCardCategory(card) {
            return card.category ? card.category.toLowerCase() : '';
        }

        /**
         * Renders the card gallery based on current filters.
         */
        function renderGallery() {
            gallery.innerHTML = '';
            const selectedColor = document.getElementById('filter-color').value.toLowerCase();
            const selectedType = document.getElementById('filter-type').value.toLowerCase();
            const selectedRarity = document.getElementById('filter-rarity').value.toLowerCase();
            const nameFilter = document.getElementById('filter-name').value.toLowerCase();
            const selectedArchetype = document.getElementById('filter-archetype').value.toLowerCase();
            const selectedAbility = document.getElementById('filter-ability').value.toLowerCase();
            const selectedCategory = document.getElementById('filter-category').value.toLowerCase();

            dummyCards.forEach(card => {
                // Apply filters
                if (nameFilter && !card.name.toLowerCase().includes(nameFilter)) return;
                if (selectedColor) {
                    const colors = Array.isArray(card.color) ? card.color.map(c => c.toLowerCase()) : [card.color ? card.color.toLowerCase() : ''];
                    if (!colors.includes(selectedColor)) return;
                }
                if (selectedCategory) {
                    if (!card.category || card.category.toLowerCase() !== selectedCategory) return;
                }
                if (selectedType) {
                    const types = Array.isArray(card.type) ? card.type.map(t => t.toLowerCase()) : [card.type ? card.type.toLowerCase() : ''];
                    if (!types.includes(selectedType)) return;
                }
                if (selectedRarity) {
                    if (card.rarity && card.rarity.toLowerCase() !== selectedRarity) return;
                }
                if (selectedArchetype) {
                    const archetypes = Array.isArray(card.archetype)
                        ? card.archetype.map(a => a.toLowerCase())
                        : [card.archetype?.toLowerCase()];
                    if (!archetypes.includes(selectedArchetype)) return;
                }
                if (selectedAbility) {
                    const abilities = Array.isArray(card.ability)
                        ? card.ability.map(a => a.toLowerCase())
                        : [card.ability?.toLowerCase()];
                    if (!abilities.includes(selectedAbility)) return;
                }
                gallery.appendChild(createCardDiv(card));
            });
        }

        // ==========================
        // === GAMEPLAY LOGIC ===
        // ==========================

        /**
         * Shuffles an array in place.
         * @param {Array} array - The array to shuffle.
         * @returns {Array} The shuffled array.
         */
        function shuffle(array) {
            for (let i = array.length - 1; i > 0; i--) {
                const j = Math.floor(Math.random() * (i + 1));
                [array[i], array[j]] = [array[j], array[i]];
            }
            return array;
        }

        /**
         * Checks if a card can be added to the current deck based on rarity limits and total card count.
         * @param {object} card - The card object to check.
         * @returns {boolean} True if the card can be added, false otherwise.
         */
        function canAddCard(card) {
            const deck = getCurrentDeck();
            const count = deck[card.id] || 0;
            const total = Object.values(deck).reduce((a, b) => a + b, 0);

            if (total >= 50) {
                // If the card is already in the deck, allow incrementing up to limit if it's the specific card being added
                if (deck[card.id] === undefined || deck[card.id] === 0) {
                    return false; // Cannot add new card if deck is full
                }
            }

            const rarity = card.rarity ? card.rarity.toLowerCase() : '';
            if (rarity === 'legendary' && count >= 1) return false;
            if (rarity === 'rare' && count >= 2) return false;
            if (rarity === 'common' && count >= 3) return false;

            return true;
        }


        /**
         * Draws N cards for a player or opponent.
         * @param {string} who - "player" or "opponent".
         * @param {number} n - Number of cards to draw.
         */
        function drawCards(who, n) {
            let deck = who === "player" ? gameState.playerDeck : gameState.opponentDeck;
            let hand = who === "player" ? gameState.playerHand : gameState.opponentHand;

            for (let i = 0; i < n && deck.length > 0; i++) {
                hand.push(deck.shift());
            }
            renderGameState();
            setupDropZones();
        }

        /**
         * Renders the current state of the game board.
         */
        function renderGameState() {
            // RENDER PLAYER HAND
            const playerHandDiv = document.getElementById('player-hand');
            playerHandDiv.innerHTML = '';
            document.getElementById('player-hand-count').textContent = gameState.playerHand.length;
            for (let cardObj of gameState.playerHand) {
                const card = dummyCards.find(c => c.id === cardObj.cardId);
                if (!card) continue;
                const div = document.createElement('div');
                div.className = 'card on-field cursor-grab'; // Added cursor-grab
                div.draggable = true;
                div.ondragstart = (e) => {
                    e.dataTransfer.setData("text/plain", cardObj.instanceId);
                    e.dataTransfer.setData("source", "hand");
                    e.dataTransfer.effectAllowed = "move"; // Indicate move operation
                };
                const img = document.createElement('img');
                img.src = card.image;
                img.alt = card.name;
                img.className = 'w-20 h-28 object-cover rounded-md'; // Tailwind for sizing
                div.appendChild(img);
                div.onclick = (e) => {
                    e.stopPropagation();
                    showHandCardMenu(cardObj.instanceId, div);
                };
                playerHandDiv.appendChild(div);
            }

            // RENDER OPPONENT HAND FACEDOWN
            const opponentHandDiv = document.getElementById('opponent-hand');
            opponentHandDiv.innerHTML = '';
            document.getElementById('opponent-hand-count').textContent = gameState.opponentHand.length;
            for (let i = 0; i < gameState.opponentHand.length; i++) {
                const div = document.createElement('div');
                div.className = 'card on-field';
                const img = document.createElement('img');
                img.src = "https://placehold.co/80x110/555/FFF?text=Card+Back"; // Use your card back image
                img.alt = "Opponent's card";
                img.className = 'w-20 h-28 object-cover rounded-md'; // Tailwind for sizing
                div.appendChild(img);
                opponentHandDiv.appendChild(div);
            }

            // Player Zones
            renderRowZone('player-creatures-zone', gameState.playerCreatures, "creature", "player");
            renderRowZone('player-domains-zone', gameState.playerDomains, "domain", "player");

            // Opponent Zones
            renderRowZone('opponent-creatures-zone', gameState.opponentCreatures, "creature", "opponent");
            renderRowZone('opponent-domains-zone', gameState.opponentDomains, "domain", "opponent");
        }

        /**
         * Displays the context menu for a card in the player's hand.
         * @param {string} instanceId - The unique instance ID of the card.
         * @param {HTMLElement} cardDiv - The HTML element of the card.
         */
        function showHandCardMenu(instanceId, cardDiv) {
            handCardMenu.setAttribute('data-instance-id', instanceId);

            const rect = cardDiv.getBoundingClientRect();
            handCardMenu.style.top = `${rect.bottom + window.scrollY + 6}px`;
            handCardMenu.style.left = `${rect.left + window.scrollX + rect.width / 2 - handCardMenu.offsetWidth / 2}px`;
            handCardMenu.style.display = 'flex'; // Use flex for column layout

            // Hide menu when clicking elsewhere
            document.body.addEventListener('click', function handler() {
                handCardMenu.style.display = 'none';
                document.body.removeEventListener('click', handler);
            }, { once: true });
        }

        /**
         * Sets up drag-and-drop functionality for battlefield zones.
         */
        function setupDropZones() {
            ['player-creatures-zone', 'player-domains-zone'].forEach(zoneId => {
                const zone = document.getElementById(zoneId);
                if (!zone) return;

                zone.ondragover = (e) => {
                    e.preventDefault();
                    e.dataTransfer.dropEffect = "move";
                    zone.classList.add('drag-over');
                };
                zone.ondragleave = () => zone.classList.remove('drag-over');
                zone.ondrop = (e) => {
                    e.preventDefault();
                    zone.classList.remove('drag-over');
                    const instanceId = e.dataTransfer.getData('text/plain');
                    const source = e.dataTransfer.getData("source");

                    let sourceArray;
                    if (source === "hand") {
                        sourceArray = gameState.playerHand;
                    } else if (source === "field-creature") {
                        sourceArray = gameState.playerCreatures;
                    } else if (source === "field-domain") {
                        sourceArray = gameState.playerDomains;
                    } else {
                        return; // Invalid source
                    }

                    // Determine target array based on zoneId
                    let targetArr;
                    if (zoneId === "player-creatures-zone") {
                        targetArr = gameState.playerCreatures;
                    } else if (zoneId === "player-domains-zone") {
                        targetArr = gameState.playerDomains;
                    } else {
                        return; // Invalid target zone for player cards
                    }

                    // Find the card object by instanceId in the source array
                    const cardIdx = sourceArray.findIndex(c => c.instanceId === instanceId);
                    if (cardIdx === -1) return;

                    const cardObj = sourceArray[cardIdx];
                    const cardData = dummyCards.find(c => c.id === cardObj.cardId);

                    // Check if the card can be played in this zone
                    if (zoneId === "player-creatures-zone" && cardData.category !== "Creature") {
                        console.log("Cannot play non-creature card in creature zone.");
                        return;
                    }
                    if (zoneId === "player-domains-zone" && cardData.category !== "Domain") {
                        console.log("Cannot play non-domain card in domain zone.");
                        return;
                    }

                    moveCard(instanceId, sourceArray, targetArr, { orientation: "vertical" });
                    renderGameState();
                    setupDropZones(); // Re-setup drop zones as elements might be re-rendered
                };
            });
        }

        /**
         * Renders a row zone on the battlefield.
         * @param {string} zoneId - The ID of the zone to render.
         * @param {Array} cardArray - The array of card objects in this zone.
         * @param {string} category - The expected category of cards for this zone (e.g., "creature", "domain").
         * @param {string} who - "player" or "opponent".
         */
        function renderRowZone(zoneId, cardArray, category, who) {
            const zoneDiv = document.getElementById(zoneId);
            zoneDiv.innerHTML = ''; // Clear existing content

            // RENDER CARDS IN ZONES
            for (const cardObj of cardArray) {
                zoneDiv.appendChild(renderCardOnField(cardObj, zoneId));
            }

            // Append Deck/Void zones at the end of player/opponent rows
            if (who === "player") {
                if (zoneId === "player-domains-zone") {
                    appendDeckZone(zoneDiv, gameState.playerDeck, "player");
                }
                if (zoneId === "player-creatures-zone") {
                    appendVoidZone(zoneDiv, gameState.playerVoid, "player");
                }
            } else if (who === "opponent") {
                if (zoneId === "opponent-domains-zone") {
                    appendDeckZone(zoneDiv, gameState.opponentDeck, "opponent");
                }
                if (zoneId === "opponent-creatures-zone") {
                    appendVoidZone(zoneDiv, gameState.opponentVoid, "opponent");
                }
            }
        }

        /**
         * Helper to create and append the deck zone card at the end of a row.
         * @param {HTMLElement} parentDiv - The parent div to append to.
         * @param {Array} deckArray - The deck array.
         * @param {string} who - "player" or "opponent".
         */
        function appendDeckZone(parentDiv, deckArray, who) {
            const deckZone = document.createElement('div');
            deckZone.className = 'deck-zone';
            const deckCard = document.createElement('div');
            deckCard.className = 'card';
            const img = document.createElement('img');
            img.src = "https://placehold.co/80x110/555/FFF?text=Deck"; // Generic card back for deck
            img.alt = who + "'s Deck";
            img.className = 'w-16 h-24 object-cover rounded-md'; // Tailwind for sizing
            img.style.opacity = "0.85";
            deckCard.appendChild(img);

            const countDiv = document.createElement('div');
            countDiv.className = 'text-center font-bold text-lg text-white mt-1';
            countDiv.textContent = deckArray.length;
            deckCard.appendChild(countDiv);
            deckZone.appendChild(deckCard);

            // Deck menu for player
            if (who === "player") {
                deckCard.onclick = (e) => {
                    e.stopPropagation();
                    const rect = deckCard.getBoundingClientRect();
                    deckActionsMenu.style.top = `${rect.bottom + window.scrollY + 8}px`;
                    deckActionsMenu.style.left = `${rect.left + window.scrollX}px`;
                    deckActionsMenu.style.display = "flex"; // Use flex for column layout

                    // Hide menu when clicking elsewhere
                    document.body.addEventListener('click', function handler() {
                        deckActionsMenu.style.display = 'none';
                        document.body.removeEventListener('click', handler);
                    }, { once: true });
                };
            }

            parentDiv.appendChild(deckZone);
        }

        /**
         * Helper to create and append the void zone card at the end of a row.
         * @param {HTMLElement} parentDiv - The parent div to append to.
         * @param {Array} voidArray - The void array.
         * @param {string} who - "player" or "opponent".
         */
        function appendVoidZone(parentDiv, voidArray, who) {
            const voidZone = document.createElement('div');
            voidZone.className = 'void-zone';
            const voidCard = document.createElement('div');
            voidCard.className = 'card';

            // Show last card image if void is not empty
            if (voidArray.length > 0) {
                const lastCardObj = voidArray[voidArray.length - 1];
                const card = dummyCards.find(c => c.id === lastCardObj.cardId);
                if (card && card.image) {
                    const img = document.createElement('img');
                    img.src = card.image;
                    img.alt = card.name;
                    img.className = 'w-16 h-24 object-cover rounded-md'; // Tailwind for sizing
                    img.style.opacity = "0.85";
                    voidCard.appendChild(img);
                }
            } else {
                 // Placeholder for empty void zone
                const img = document.createElement('img');
                img.src = "https://placehold.co/80x110/333/FFF?text=Void";
                img.alt = "Empty Void";
                img.className = 'w-16 h-24 object-cover rounded-md';
                img.style.opacity = "0.6";
                voidCard.appendChild(img);
            }

            const countDiv = document.createElement('div');
            countDiv.className = 'text-center font-bold text-lg text-white mt-1';
            countDiv.textContent = voidArray.length;
            voidCard.appendChild(countDiv);
            voidZone.appendChild(voidCard);

            voidCard.onclick = (e) => {
                e.stopPropagation();
                showVoidModal();
            };

            parentDiv.appendChild(voidZone);
        }

        /**
         * Moves a card from a source array to a target array.
         * @param {string} instanceId - The unique instance ID of the card.
         * @param {Array} sourceArr - The array to remove the card from.
         * @param {Array} targetArr - The array to add the card to.
         * @param {object} [options={}] - Optional properties to add to the card object (e.g., orientation).
         */
        function moveCard(instanceId, sourceArr, targetArr, options = {}) {
            const cardIdx = sourceArr.findIndex(c => c.instanceId === instanceId);
            if (cardIdx === -1) {
                console.error("Card not found in source array:", instanceId, sourceArr);
                return;
            }

            const [cardObj] = sourceArr.splice(cardIdx, 1);
            targetArr.push({ ...cleanCard(cardObj), ...options }); // Add cleaned card with new options
        }

        /**
         * Cleans a card object by removing temporary gameplay stats.
         * @param {object} cardObj - The card object to clean.
         * @returns {object} A new card object with only base properties.
         */
        function cleanCard(cardObj) {
            const cleaned = { ...cardObj };
            delete cleaned.currentHP;
            delete cleaned.orientation;
            return cleaned;
        }

        /**
         * Displays the full card details in a modal.
         * @param {object} cardObj - The card object (can be from dummyCards or gameState).
         */
        function showFullCardModal(cardObj) {
            const card = dummyCards.find(c => c.id === (cardObj.cardId || cardObj.id));
            if (!card) return;

            imageModalImg.src = card.image;
            imageModalImg.alt = card.name;

            imageModalContent.querySelector('h2').textContent = card.name;
            imageModalContent.querySelector('div:nth-of-type(1)').innerHTML = [
                card.hp !== undefined ? `HP: ${card.hp}` : '',
                card.atk !== undefined ? ` | ATK: ${card.atk}` : '',
                card.def !== undefined ? ` | DEF: ${card.def}` : '',
                card.cost !== undefined ? ` | Cost: ${card.cost}` : ''
            ].filter(Boolean).join('');
            imageModalContent.querySelector('div:nth-of-type(2)').textContent =
                `${card.rarity || ''} ${Array.isArray(card.type) ? card.type.join(', ') : card.type || ''}`.trim();
            imageModalContent.querySelector('div:nth-of-type(3)').textContent = card.text || '';

            imageModal.style.display = 'flex'; // Use flex to center
        }

        /**
         * Gets the base HP of a card.
         * @param {string} cardId - The ID of the card.
         * @returns {number} The base HP, or 1 if not found.
         */
        function getBaseHp(cardId) {
            const card = dummyCards.find(c => c.id === cardId);
            return card ? card.hp : 1; // fallback to 1 if not found
        }

        /**
         * Renders a card for display on the battlefield.
         * @param {object} cardObj - The card object with instanceId and gameplay stats.
         * @param {string} zoneId - The ID of the zone the card is in.
         * @returns {HTMLElement} The created card div for the field.
         */
        function renderCardOnField(cardObj, zoneId) {
            // Initialize currentHP if not set
            if (typeof cardObj.currentHP !== "number") {
                cardObj.currentHP = getBaseHp(cardObj.cardId);
            }
            const baseHP = getBaseHp(cardObj.cardId);
            const currentHP = cardObj.currentHP;
            const hpPercent = Math.max(0, Math.min(1, currentHP / baseHP));

            // Color logic for HP bar
            let barColor = "#4caf50"; // green
            if (hpPercent <= 0.25) {
                barColor = "#e53935"; // red
            } else if (hpPercent <= 0.5) {
                barColor = "#ff9800"; // orange
            }

            // Create the main card div
            const cardDiv = document.createElement('div');
            cardDiv.className = 'card on-field';
            cardDiv.dataset.instanceId = cardObj.instanceId;
            cardDiv.draggable = true; // Make cards on field draggable
            cardDiv.ondragstart = (e) => {
                e.dataTransfer.setData("text/plain", cardObj.instanceId);
                // Indicate source zone for valid drops
                if (zoneId === "player-creatures-zone") {
                    e.dataTransfer.setData("source", "field-creature");
                } else if (zoneId === "player-domains-zone") {
                    e.dataTransfer.setData("source", "field-domain");
                }
                e.dataTransfer.effectAllowed = "move";
            };

            // Add card image
            const cardData = dummyCards.find(c => c.id === cardObj.cardId);
            if (cardData && cardData.image) {
                const img = document.createElement('img');
                img.src = cardData.image;
                img.alt = cardData.name || "Card";
                img.className = 'w-20 h-28 object-cover rounded-md'; // Tailwind for sizing
                if (cardObj.orientation === "horizontal") {
                    img.style.transform = "rotate(90deg)";
                }
                cardDiv.appendChild(img);
            } else {
                // fallback if no image
                cardDiv.textContent = cardData ? cardData.name : "Unknown";
                cardDiv.className += ' flex items-center justify-center'; // Center text for fallback
            }

            // HP BADGE (only for creatures)
            if (cardData && cardData.category === "Creature") {
                const hpBadge = document.createElement('span');
                hpBadge.className = 'hp-badge';
                hpBadge.textContent = `(${cardObj.currentHP})`;
                cardDiv.appendChild(hpBadge);

                // HP Bar
                const barWrap = document.createElement('div');
                barWrap.className = 'hp-bar-wrap';
                const bar = document.createElement('div');
                bar.className = 'hp-bar';
                bar.style.width = `${Math.round(hpPercent * 100)}%`;
                bar.style.backgroundColor = barColor;
                barWrap.appendChild(bar);
                cardDiv.appendChild(barWrap);
            }

            // Click handler for card actions
            cardDiv.onclick = function(e) {
                e.stopPropagation();
                showCardActionMenu(cardObj.instanceId, zoneId, cardObj.orientation || "vertical", cardDiv);
            };
            return cardDiv;
        }

        /**
         * Displays the context menu for a card on the battlefield.
         * @param {string} instanceId - The unique instance ID of the card.
         * @param {string} zoneId - The ID of the zone the card is in.
         * @param {string} currentOrientation - The current orientation ("vertical" or "horizontal").
         * @param {HTMLElement} cardDiv - The HTML element of the card.
         */
        function showCardActionMenu(instanceId, zoneId, currentOrientation, cardDiv) {
            fieldCardMenu.setAttribute('data-instance-id', instanceId);
            fieldCardMenu.setAttribute('data-zone-id', zoneId);

            // Toggle rotate button text
            rotateCardBtn.textContent = currentOrientation === "vertical" ? "Rotate Horizontal" : "Rotate Vertical";

            const rect = cardDiv.getBoundingClientRect();
            fieldCardMenu.style.top = `${rect.bottom + window.scrollY + 6}px`;
            fieldCardMenu.style.left = `${rect.left + window.scrollX + rect.width / 2 - fieldCardMenu.offsetWidth / 2}px`;
            fieldCardMenu.style.display = 'flex'; // Use flex for column layout

            // Hide menu when clicking elsewhere
            document.body.addEventListener('click', function handler() {
                fieldCardMenu.style.display = 'none';
                document.body.removeEventListener('click', handler);
            }, { once: true });
        }


        // ==========================
        // === Custom Modals (Replacing prompt/confirm) ===
        // ==========================

        let resolveInputModal;
        let resolveConfirmModal;

        /**
         * Shows a custom input modal.
         * @param {string} title - The title of the modal.
         * @param {string} message - The message displayed in the modal.
         * @param {string} defaultValue - The default value for the input field.
         * @returns {Promise<string|null>} A promise that resolves with the input value or null if cancelled.
         */
        function showInputModal(title, message, defaultValue = '') {
            return new Promise(resolve => {
                inputModalTitle.textContent = title;
                inputModalMessage.textContent = message;
                inputModalField.value = defaultValue;
                inputModal.style.display = 'flex';
                inputModalField.focus();

                const handleConfirm = () => {
                    inputModal.style.display = 'none';
                    resolve(inputModalField.value);
                    inputModalConfirmBtn.removeEventListener('click', handleConfirm);
                    inputModalCancelBtn.removeEventListener('click', handleCancel);
                };

                const handleCancel = () => {
                    inputModal.style.display = 'none';
                    resolve(null);
                    inputModalConfirmBtn.removeEventListener('click', handleConfirm);
                    inputModalCancelBtn.removeEventListener('click', handleCancel);
                };

                inputModalConfirmBtn.addEventListener('click', handleConfirm);
                inputModalCancelBtn.addEventListener('click', handleCancel);

                // Allow pressing Enter to confirm
                inputModalField.onkeydown = (e) => {
                    if (e.key === 'Enter') {
                        e.preventDefault();
                        handleConfirm();
                    }
                };

                // Click outside to cancel
                inputModal.onclick = (e) => {
                    if (e.target === inputModal) {
                        handleCancel();
                    }
                };
            });
        }

        /**
         * Shows a custom confirmation modal.
         * @param {string} title - The title of the modal.
         * @param {string} message - The message displayed in the modal.
         * @returns {Promise<boolean>} A promise that resolves to true if confirmed, false otherwise.
         */
        function showConfirmModal(title, message) {
            return new Promise(resolve => {
                confirmModalTitle.textContent = title;
                confirmModalMessage.textContent = message;
                confirmModal.style.display = 'flex';

                const handleOk = () => {
                    confirmModal.style.display = 'none';
                    resolve(true);
                    confirmModalOkBtn.removeEventListener('click', handleOk);
                    confirmModalCancelBtn.removeEventListener('click', handleCancel);
                };

                const handleCancel = () => {
                    confirmModal.style.display = 'none';
                    resolve(false);
                    confirmModalOkBtn.removeEventListener('click', handleOk);
                    confirmModalCancelBtn.removeEventListener('click', handleCancel);
                };

                confirmModalOkBtn.addEventListener('click', handleOk);
                confirmModalCancelBtn.addEventListener('click', handleCancel);

                // Click outside to cancel
                confirmModal.onclick = (e) => {
                    if (e.target === confirmModal) {
                        handleCancel();
                    }
                };
            });
        }

        // ==========================
        // === MODAL DISPLAY LOGIC (Old Functionality Re-integrated) ===
        // ==========================

        /**
         * Opens the deck search modal, populating it with cards from the player's deck.
         */
        function openDeckSearchModal() {
            deckSearchContent.innerHTML = "<h3>Select a card and choose an action:</h3>";
            let deckMenu = document.getElementById('deck-search-card-menu');
            if (deckMenu) deckMenu.remove(); // Remove any old menu

            gameState.playerDeck.forEach((cardObj, idx) => {
                const card = dummyCards.find(c => c.id === cardObj.cardId);
                if (!card) return;

                const btn = document.createElement('button');
                btn.className = 'card card-modal-dark flex flex-col items-center justify-center p-2 bg-gray-700 text-white rounded-lg shadow transition duration-200 hover:bg-gray-600';
                btn.style.width = "110px";
                btn.style.height = "170px";

                const img = document.createElement('img');
                img.src = card.image;
                img.alt = card.name;
                img.className = 'max-w-[80px] max-h-[110px] block mb-2 rounded-md';

                const name = document.createElement('div');
                name.textContent = card.name;
                name.className = "card-name text-xs font-semibold text-center";

                btn.appendChild(img);
                btn.appendChild(name);

                btn.onclick = (e) => {
                    e.stopPropagation();
                    let oldMenu = document.getElementById('deck-search-card-menu');
                    if (oldMenu) oldMenu.remove();

                    const menu = document.createElement('div');
                    menu.id = 'deck-search-card-menu';
                    menu.className = 'absolute bg-gray-800 border border-gray-600 rounded-lg shadow-xl p-2 z-50 flex flex-col gap-1';
                    menu.innerHTML = `
                        <button type="button" class="deck-action-add bg-blue-600 hover:bg-blue-700 text-white py-1 px-3 rounded text-sm">Add to Hand</button>
                        <button type="button" class="deck-action-void bg-red-600 hover:bg-red-700 text-white py-1 px-3 rounded text-sm">Send to Void</button>
                        <button type="button" class="deck-action-view bg-gray-600 hover:bg-gray-700 text-white py-1 px-3 rounded text-sm">View</button>
                    `;

                    const rect = btn.getBoundingClientRect();
                    menu.style.top = `${rect.bottom + window.scrollY + 4}px`;
                    menu.style.left = `${rect.left + window.scrollX}px`;

                    menu.querySelector('.deck-action-add').onclick = (ev) => {
                        ev.stopPropagation();
                        moveCard(cardObj.instanceId, gameState.playerDeck, gameState.playerHand);
                        renderGameState();
                        setupDropZones();
                        menu.remove();
                        openDeckSearchModal(); // Refresh modal content
                    };
                    menu.querySelector('.deck-action-void').onclick = (ev) => {
                        ev.stopPropagation();
                        moveCard(cardObj.instanceId, gameState.playerDeck, gameState.playerVoid);
                        renderGameState();
                        setupDropZones();
                        menu.remove();
                        openDeckSearchModal(); // Refresh modal content
                    };
                    menu.querySelector('.deck-action-view').onclick = (ev) => {
                        ev.stopPropagation();
                        showFullCardModal(cardObj);
                        menu.remove();
                    };

                    document.body.appendChild(menu);

                    setTimeout(() => {
                        document.body.addEventListener('click', function handler() {
                            if (menu) menu.remove();
                            document.body.removeEventListener('click', handler);
                        }, { once: true });
                    }, 10);
                };
                deckSearchContent.appendChild(btn);
            });
            deckSearchModal.style.display = "flex";
        }

        /**
         * Closes the deck search modal.
         */
        function closeDeckSearchModal() {
            deckSearchModal.style.display = "none";
        }

        /**
         * Displays the contents of the void zone in a modal.
         */
        function showVoidModal() {
            voidCardList.innerHTML = '';
            const voidCards = gameState.playerVoid;

            if (voidCards.length === 0) {
                voidCardList.innerHTML = '<div class="text-gray-500 text-center col-span-full py-4">Void is empty.</div>';
            } else {
                voidCards.forEach((cardObj, idx) => {
                    const card = dummyCards.find(c => c.id === cardObj.cardId);
                    if (!card) return;

                    const btn = document.createElement('button');
                    btn.className = 'card card-modal-dark flex flex-col items-center justify-center p-2 bg-gray-700 text-white rounded-lg shadow transition duration-200 hover:bg-gray-600';
                    btn.style.width = "110px";
                    btn.style.height = "170px";

                    const img = document.createElement('img');
                    img.src = card.image;
                    img.alt = card.name;
                    img.className = 'max-w-[80px] max-h-[110px] block mb-2 rounded-md';

                    const name = document.createElement('div');
                    name.textContent = card.name;
                    name.className = "text-xs font-semibold text-center";

                    btn.appendChild(img);
                    btn.appendChild(name);

                    btn.onclick = (e) => {
                        e.stopPropagation();
                        // For void, just view the card for now
                        showFullCardModal(cardObj);
                    };
                    voidCardList.appendChild(btn);
                });
            }
            voidModal.style.display = 'flex';
        }

        /**
         * Shows the loading overlay with a message.
         * @param {string} message - The message to display.
         */
        function showLoadingOverlay(message = "Loading...") {
            loadingOverlay.querySelector('p').textContent = message;
            loadingOverlay.classList.remove('hidden');
            loadingOverlay.classList.add('flex');
        }

        /**
         * Hides the loading overlay.
         */
        function hideLoadingOverlay() {
            loadingOverlay.classList.add('hidden');
            loadingOverlay.classList.remove('flex');
        }

        // ==========================
        // === EVENT LISTENERS ===
        // ==========================

        // DOMContentLoaded is important to ensure all elements are loaded before
        // we try to access them. Initialize Firebase inside this.
        window.addEventListener('DOMContentLoaded', initializeFirebase);

        // Deck slot events
        deckSlotSelect.addEventListener('change', () => {
            currentDeckSlot = deckSlotSelect.value;
            saveCurrentDeckSlotToFirestore(currentDeckSlot); // Save just the current slot
            updateDeckDisplay(); // Update display with new current deck
            renderGallery(); // Re-render gallery to update add button states
        });

        addDeckSlotBtn.addEventListener('click', async () => {
            const newName = await showInputModal("Add New Deck Slot", "Enter a name for the new deck slot:", `Deck ${deckSlots.length + 1}`);
            if (newName && newName.trim() !== "" && !deckSlots.includes(newName.trim())) {
                deckSlots.push(newName.trim());
                decks[newName.trim()] = {}; // Initialize new empty deck
                currentDeckSlot = newName.trim();
                await saveDeckState(); // Save all state to Firestore
                saveCurrentDeckSlotToFirestore(currentDeckSlot); // Save current slot specifically
                refreshDeckSlotSelect();
                updateDeckDisplay();
                renderGallery();
            } else if (newName && deckSlots.includes(newName.trim())) {
                showConfirmModal("Error", "Deck slot with this name already exists.").then(() => {}); // Re-using confirm modal as a simple alert
            }
        });

        renameDeckSlotBtn.addEventListener('click', async () => {
            if (deckSlots.length === 0) {
                showConfirmModal("Error", "No deck slots to rename.").then(() => {});
                return;
            }
            const oldName = currentDeckSlot;
            const newName = await showInputModal("Rename Deck Slot", `Rename "${oldName}" to:`, oldName);
            if (newName && newName.trim() !== "" && newName.trim() !== oldName) {
                if (deckSlots.includes(newName.trim())) {
                    showConfirmModal("Error", "Deck slot with this new name already exists.").then(() => {});
                    return;
                }

                const oldIndex = deckSlots.indexOf(oldName);
                if (oldIndex !== -1) {
                    // Update deckSlots array
                    deckSlots[oldIndex] = newName.trim();

                    // Rename the deck data in the 'decks' object
                    const oldDeckData = decks[oldName];
                    delete decks[oldName];
                    decks[newName.trim()] = oldDeckData || {};

                    currentDeckSlot = newName.trim();
                    await saveDeckState(); // Save all state to Firestore

                    // Delete the old document in Firestore
                    try {
                        const oldDeckDocRef = doc(db, `artifacts/${appId}/users/${userId}/decks/${oldName}`);
                        await deleteDoc(oldDeckDocRef);
                        console.log(`Old deck document '${oldName}' deleted from Firestore.`);
                    } catch (error) {
                        console.error("Error deleting old deck document:", error);
                    }

                    refreshDeckSlotSelect();
                    updateDeckDisplay();
                    renderGallery();
                }
            }
        });

        deleteDeckSlotBtn.addEventListener('click', async () => {
            if (deckSlots.length === 0) {
                showConfirmModal("Error", "No deck slots to delete.").then(() => {});
                return;
            }
            const slotToDelete = currentDeckSlot;
            const confirm = await showConfirmModal("Delete Deck Slot", `Are you sure you want to delete the deck slot "${slotToDelete}"? This action cannot be undone.`);
            if (confirm) {
                // Remove from deckSlots array
                deckSlots = deckSlots.filter(slot => slot !== slotToDelete);
                delete decks[slotToDelete]; // Remove deck data

                // Set new currentDeckSlot
                currentDeckSlot = deckSlots.length > 0 ? deckSlots[0] : "Deck 1";
                if (deckSlots.length === 0) {
                    deckSlots = ["Deck 1"]; // Re-initialize with default if all deleted
                    decks["Deck 1"] = {};
                    currentDeckSlot = "Deck 1";
                }

                await saveDeckState(); // Save all state to Firestore

                // Delete the corresponding document in Firestore
                try {
                    const deckDocRef = doc(db, `artifacts/${appId}/users/${userId}/decks/${slotToDelete}`);
                    await deleteDoc(deckDocRef);
                    console.log(`Deck document '${slotToDelete}' deleted from Firestore.`);
                } catch (error) {
                    console.error("Error deleting deck document:", error);
                }

                refreshDeckSlotSelect();
                updateDeckDisplay();
                renderGallery();
            }
        });

        // Filter events
        document.getElementById('filter-name').addEventListener('input', renderGallery);
        document.getElementById('filter-color').addEventListener('change', renderGallery);
        document.getElementById('filter-type').addEventListener('change', renderGallery);
        document.getElementById('filter-rarity').addEventListener('change', renderGallery);
        document.getElementById('filter-archetype').addEventListener('input', renderGallery);
        document.getElementById('filter-ability').addEventListener('input', renderGallery);
        document.getElementById('filter-category').addEventListener('change', renderGallery);

        // Page Navigation
        startGameBtn.addEventListener('click', () => {
            const currentDeck = getCurrentDeck();
            const totalCards = Object.values(currentDeck).reduce((a, b) => a + b, 0);
            if (totalCards < 1) { // Changed to 1 for easier testing, adjust to 50 for actual game
                showConfirmModal("Cannot Start Game", `Your deck has ${totalCards} cards. A valid deck must have at least 1 card.`).then(() => {});
                return;
            }
            // Build the player's deck from the currentDeck object for gameplay
            gameState.playerDeck = shuffle(buildDeck(currentDeck));
            gameState.playerHand = [];
            gameState.playerCreatures = [];
            gameState.playerDomains = [];
            gameState.playerVoid = [];
            // Reset opponent state for a new game
            gameState.opponentDeck = shuffle(buildDeck(currentDeck)); // Opponent uses same deck for now
            gameState.opponentHand = [];
            gameState.opponentCreatures = [];
            gameState.opponentDomains = [];
            gameState.opponentVoid = [];

            gameState.turn = "player";
            gameState.phase = "draw";
            gameState.phaseIndex = 0;

            drawCards("player", 5); // Draw initial hand for player
            drawCards("opponent", 5); // Draw initial hand for opponent

            showBattlefield();
            updatePhaseBar();
            setupDropZones();
        });

        backToBuilderBtn.addEventListener('click', showBuilder);

        // Toggle Deck Panel
        toggleDeckBtn.addEventListener('click', () => {
            deckPanel.classList.toggle('hidden'); // Use hidden class for visibility toggle
        });

        // Gameplay Phase Progression
        nextPhaseBtn.addEventListener('click', () => {
            gameState.phaseIndex = (gameState.phaseIndex + 1) % PHASES.length;
            gameState.turn = PHASES[gameState.phaseIndex].turn;
            gameState.phase = PHASES[gameState.phaseIndex].phase;
            updatePhaseBar();
        });

        // Modals general close
        closeImageModalBtn.onclick = () => { imageModal.style.display = "none"; };
        imageModal.onclick = (e) => { if (e.target === imageModal) imageModal.style.display = "none"; };

        closeDeckSearchModalBtn.onclick = closeDeckSearchModal;
        deckSearchModal.onclick = (e) => { if (e.target === deckSearchModal) closeDeckSearchModal(); };

        closeVoidModalBtn.onclick = () => { voidModal.style.display = "none"; };
        voidModal.addEventListener('click', (event) => {
            if (event.target === voidModal) voidModal.style.display = 'none';
        });

        // Hand Card Menu Actions
        playCreatureBtn.addEventListener('click', () => {
            const instanceId = handCardMenu.getAttribute('data-instance-id');
            const cardObj = gameState.playerHand.find(c => c.instanceId === instanceId);
            const cardData = dummyCards.find(c => c.id === cardObj.cardId);

            if (cardData.category === "Creature") {
                moveCard(instanceId, gameState.playerHand, gameState.playerCreatures, { orientation: "vertical" });
                renderGameState();
                setupDropZones();
            } else {
                showConfirmModal("Cannot Play Creature", "This card is not a creature.").then(() => {});
            }
            handCardMenu.style.display = 'none';
        });

        playDomainBtn.addEventListener('click', () => {
            const instanceId = handCardMenu.getAttribute('data-instance-id');
            const cardObj = gameState.playerHand.find(c => c.instanceId === instanceId);
            const cardData = dummyCards.find(c => c.id === cardObj.cardId);

            if (cardData.category === "Domain") {
                moveCard(instanceId, gameState.playerHand, gameState.playerDomains, { orientation: "vertical" });
                renderGameState();
                setupDropZones();
            } else {
                showConfirmModal("Cannot Play Domain", "This card is not a domain.").then(() => {});
            }
            handCardMenu.style.display = 'none';
        });

        sendToVoidHandBtn.addEventListener('click', () => {
            const instanceId = handCardMenu.getAttribute('data-instance-id');
            moveCard(instanceId, gameState.playerHand, gameState.playerVoid);
            renderGameState();
            setupDropZones();
            handCardMenu.style.display = 'none';
        });

        // Deck Actions Menu
        drawCardBtn.addEventListener('click', () => {
            drawCards("player", 1);
            deckActionsMenu.style.display = 'none';
        });

        searchDeckBtn.addEventListener('click', () => {
            openDeckSearchModal();
            deckActionsMenu.style.display = 'none';
        });

        shuffleDeckBtn.addEventListener('click', () => {
            shuffle(gameState.playerDeck);
            renderGameState(); // Re-render to update deck count visual
            deckActionsMenu.style.display = 'none';
        });

        // Field Card Action Menu
        rotateCardBtn.addEventListener('click', () => {
            const instanceId = fieldCardMenu.getAttribute('data-instance-id');
            const zoneId = fieldCardMenu.getAttribute('data-zone-id');
            const zoneArray = getZoneArray(zoneId);
            if (!zoneArray) {
                console.error("Invalid zone for rotation.");
                return;
            }

            const cardObj = zoneArray.find(c => c.instanceId === instanceId);
            if (cardObj) {
                cardObj.orientation = cardObj.orientation === "vertical" ? "horizontal" : "vertical";
                renderGameState();
            }
            fieldCardMenu.style.display = 'none';
        });

        addHpBtn.addEventListener('click', async () => {
            const instanceId = fieldCardMenu.getAttribute('data-instance-id');
            const zoneId = fieldCardMenu.getAttribute('data-zone-id');
            const zoneArray = getZoneArray(zoneId);
            const cardObj = zoneArray.find(c => c.instanceId === instanceId);
            const cardData = dummyCards.find(c => c.id === cardObj.cardId);

            if (cardData.category === "Creature") {
                const amountStr = await showInputModal("Add HP", "Enter amount to add:", "1");
                const amount = parseInt(amountStr);
                if (!isNaN(amount) && amount > 0) {
                    cardObj.currentHP = (cardObj.currentHP || 0) + amount;
                    renderGameState();
                } else if (amountStr !== null) {
                    showConfirmModal("Invalid Input", "Please enter a positive number.").then(() => {});
                }
            } else {
                showConfirmModal("Cannot Adjust HP", "Only creatures have HP.").then(() => {});
            }
            fieldCardMenu.style.display = 'none';
        });

        removeHpBtn.addEventListener('click', async () => {
            const instanceId = fieldCardMenu.getAttribute('data-instance-id');
            const zoneId = fieldCardMenu.getAttribute('data-zone-id');
            const zoneArray = getZoneArray(zoneId);
            const cardObj = zoneArray.find(c => c.instanceId === instanceId);
            const cardData = dummyCards.find(c => c.id === cardObj.cardId);

            if (cardData.category === "Creature") {
                const amountStr = await showInputModal("Remove HP", "Enter amount to remove:", "1");
                const amount = parseInt(amountStr);
                if (!isNaN(amount) && amount > 0) {
                    cardObj.currentHP = Math.max(0, (cardObj.currentHP || 0) - amount);
                    renderGameState();
                } else if (amountStr !== null) {
                    showConfirmModal("Invalid Input", "Please enter a positive number.").then(() => {});
                }
            } else {
                showConfirmModal("Cannot Adjust HP", "Only creatures have HP.").then(() => {});
            }
            fieldCardMenu.style.display = 'none';
        });

        sendToVoidFieldBtn.addEventListener('click', () => {
            const instanceId = fieldCardMenu.getAttribute('data-instance-id');
            const zoneId = fieldCardMenu.getAttribute('data-zone-id');
            const zoneArray = getZoneArray(zoneId);

            if (zoneArray) {
                moveCard(instanceId, zoneArray, gameState.playerVoid);
                renderGameState();
                setupDropZones();
            } else {
                console.error("Could not find source zone for card to void.");
            }
            fieldCardMenu.style.display = 'none';
        });

        // Initial render calls
        refreshDeckSlotSelect(); // Initialize select with default
        updateDeckDisplay(); // Initialize deck list with default
        renderGallery(); // Populate gallery

        // Ensure builder is shown initially
        showBuilder();

        // DECK CREATION LOGIC - Helper function to build a full deck from the count object
        function buildDeck(deckObj) {
            let deck = [];
            let uid = 1;
            for (let [cardId, count] of Object.entries(deckObj)) {
                for (let i = 0; i < count; i++) {
                    deck.push({
                        cardId: cardId,
                        instanceId: cardId + "#" + (uid++) // Unique instance ID for gameplay
                    });
                }
            }
            return deck;
        }

    </script>
</body>
</html>
