# Unit-Converter
You can convert all type of units
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Google-Style Unit Converter</title>
    <!-- Load Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Configure Tailwind to use Inter font and rounded corners -->
    <script>
        tailwind.config = {
            theme: {
                extend: {
                    fontFamily: {
                        sans: ['Inter', 'sans-serif'],
                    },
                    colors: {
                        'google-blue': '#4285F4',
                        'google-gray-light': '#F1F3F4',
                        'google-gray-dark': '#70757A',
                    }
                }
            }
        }
    </script>
    <style>
        /* Custom styles to mimic the Google search result card look */
        body {
            background-color: #f8f8f8; /* Light gray background */
        }
        .converter-card {
            box-shadow: 0 1px 3px 0 rgba(0, 0, 0, 0.08), 0 4px 6px 2px rgba(0, 0, 0, 0.05);
            border: 1px solid #dadce0;
        }
        input[type="number"]::-webkit-inner-spin-button,
        input[type="number"]::-webkit-outer-spin-button {
            -webkit-appearance: none;
            margin: 0;
        }
        input[type="number"] {
            -moz-appearance: textfield;
        }
    </style>
</head>
<body class="font-sans min-h-screen flex flex-col items-center p-4 sm:p-8">

    <!-- Search Bar Mimic -->
    <header class="w-full max-w-lg mb-8">
        <div class="flex items-center bg-white rounded-full p-3 shadow-md border border-google-gray-light">
            <svg class="w-5 h-5 text-google-gray-dark mr-2" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z"></path></svg>
            <span class="text-xl text-gray-800">Unit Converter</span>
        </div>
    </header>

    <!-- Converter Card -->
    <main id="converter-app" class="w-full max-w-lg bg-white rounded-xl converter-card overflow-hidden">
        
        <!-- Category Selector -->
        <div class="p-4 border-b border-google-gray-light">
            <label for="category-select" class="block text-sm font-medium text-google-gray-dark mb-1">Select Category</label>
            <select id="category-select" class="w-full p-3 border border-gray-300 rounded-lg focus:ring-google-blue focus:border-google-blue cursor-pointer transition duration-150 ease-in-out">
                <option value="length">Length</option>
                <option value="mass">Mass</option>
                <option value="temperature">Temperature</option>
                <option value="volume">Volume</option>
            </select>
        </div>

        <!-- Conversion Interface -->
        <div class="p-5 space-y-4">
            <!-- Source Input Group -->
            <div class="flex items-center space-x-2">
                <!-- Value Input -->
                <div class="flex-grow">
                    <label for="source-value" class="sr-only">Input Value</label>
                    <input type="number" id="source-value" value="1" placeholder="Enter value" class="w-full p-3 text-2xl font-semibold border-b-2 border-google-blue focus:outline-none focus:border-google-blue transition duration-150 ease-in-out rounded-t-lg" style="box-shadow: none;">
                </div>
                <!-- Source Unit Selector -->
                <div class="w-1/3">
                    <label for="source-unit" class="sr-only">From Unit</label>
                    <select id="source-unit" class="w-full p-3 border border-gray-300 rounded-lg focus:ring-google-blue focus:border-google-blue cursor-pointer"></select>
                </div>
            </div>

            <!-- Equal Sign / Separator -->
            <div class="flex justify-center py-2">
                <div class="w-10 h-1 border-b border-gray-300"></div>
            </div>

            <!-- Result Output Group -->
            <div class="flex items-center space-x-2">
                <!-- Result Display -->
                <div class="flex-grow">
                    <label for="target-result" class="sr-only">Result</label>
                    <div id="target-result" class="w-full p-3 text-2xl font-semibold text-gray-700 bg-google-gray-light rounded-lg h-full flex items-center min-h-[52px]">1.00</div>
                </div>
                <!-- Target Unit Selector -->
                <div class="w-1/3">
                    <label for="target-unit" class="sr-only">To Unit</label>
                    <select id="target-unit" class="w-full p-3 border border-gray-300 rounded-lg focus:ring-google-blue focus:border-google-blue cursor-pointer"></select>
                </div>
            </div>
        </div>

        <!-- Detailed Result (Google-style) -->
        <div class="p-5 pt-0">
             <div id="detailed-result" class="text-sm text-google-gray-dark min-h-[1.25rem]">
                1 meter equals 1.00000 meter
            </div>
        </div>
    </main>

    <script>
        // Global variables for Firebase access (required by platform but not used for this client-side tool)
        const __app_id = 'unit-converter-app';
        const __firebase_config = '{}';
        const __initial_auth_token = undefined;
        // setLogLevel('debug'); // Disabled for clean console

        const units = {
            length: {
                meter: { name: "Meter", factor: 1 },
                kilometer: { name: "Kilometer", factor: 1000 },
                mile: { name: "Mile", factor: 1609.34 },
                foot: { name: "Foot", factor: 0.3048 },
                inch: { name: "Inch", factor: 0.0254 },
                centimeter: { name: "Centimeter", factor: 0.01 },
            },
            mass: {
                kilogram: { name: "Kilogram", factor: 1 },
                gram: { name: "Gram", factor: 0.001 },
                pound: { name: "Pound", factor: 0.453592 },
                ounce: { name: "Ounce", factor: 0.0283495 },
            },
            temperature: {
                celsius: { name: "Celsius", factor: 1 },
                fahrenheit: { name: "Fahrenheit", factor: 1 },
                kelvin: { name: "Kelvin", factor: 1 },
            },
            volume: {
                liter: { name: "Liter", factor: 1 },
                milliliter: { name: "Milliliter", factor: 0.001 },
                gallon_us: { name: "US Gallon", factor: 3.78541 },
                cubic_meter: { name: "Cubic Meter", factor: 1000 },
            }
        };

        const elements = {
            category: document.getElementById('category-select'),
            sourceValue: document.getElementById('source-value'),
            sourceUnit: document.getElementById('source-unit'),
            targetUnit: document.getElementById('target-unit'),
            targetResult: document.getElementById('target-result'),
            detailedResult: document.getElementById('detailed-result'),
        };

        let currentCategory = 'length';

        /**
         * Populates the unit dropdowns based on the selected category.
         */
        function populateUnitDropdowns() {
            const categoryUnits = units[currentCategory];
            const unitKeys = Object.keys(categoryUnits);

            // Clear existing options
            elements.sourceUnit.innerHTML = '';
            elements.targetUnit.innerHTML = '';

            unitKeys.forEach(key => {
                const name = categoryUnits[key].name;

                const sourceOption = document.createElement('option');
                sourceOption.value = key;
                sourceOption.textContent = name;
                elements.sourceUnit.appendChild(sourceOption);

                const targetOption = document.createElement('option');
                targetOption.value = key;
                targetOption.textContent = name;
                elements.targetUnit.appendChild(targetOption);
            });

            // Set default selections
            elements.sourceUnit.value = unitKeys[0];
            elements.targetUnit.value = unitKeys[1] || unitKeys[0]; // Select the second unit as target if available
        }

        /**
         * Handles temperature conversions (requires offset logic).
         * @param {number} value The input value.
         * @param {string} fromUnit The source unit key.
         * @param {string} toUnit The target unit key.
         * @returns {number} The converted value.
         */
        function convertTemperature(value, fromUnit, toUnit) {
            let celsius;

            // Step 1: Convert all to Celsius (the intermediate base)
            if (fromUnit === 'celsius') {
                celsius = value;
            } else if (fromUnit === 'fahrenheit') {
                celsius = (value - 32) * 5/9;
            } else if (fromUnit === 'kelvin') {
                celsius = value - 273.15;
            } else {
                return NaN; // Should not happen
            }

            // Step 2: Convert from Celsius to the target unit
            if (toUnit === 'celsius') {
                return celsius;
            } else if (toUnit === 'fahrenheit') {
                return (celsius * 9/5) + 32;
            } else if (toUnit === 'kelvin') {
                return celsius + 273.15;
            } else {
                return NaN; // Should not happen
            }
        }

        /**
         * Handles standard multiplicative conversions (Length, Mass, Volume).
         * @param {number} value The input value.
         * @param {string} fromUnit The source unit key.
         * @param {string} toUnit The target unit key.
         * @returns {number} The converted value.
         */
        function convertStandard(value, fromUnit, toUnit) {
            const categoryUnits = units[currentCategory];
            const fromFactor = categoryUnits[fromUnit].factor;
            const toFactor = categoryUnits[toUnit].factor;

            // Convert to base unit (factor is the multiplier to reach the base unit)
            const valueInBase = value * fromFactor;

            // Convert from base unit to target unit (divide by target unit's factor)
            return valueInBase / toFactor;
        }

        /**
         * Main conversion routine.
         */
        function performConversion() {
            const value = parseFloat(elements.sourceValue.value);
            const fromUnitKey = elements.sourceUnit.value;
            const toUnitKey = elements.targetUnit.value;

            if (isNaN(value)) {
                elements.targetResult.textContent = 'Invalid Input';
                elements.detailedResult.textContent = 'Please enter a valid number.';
                return;
            }

            let result;
            if (currentCategory === 'temperature') {
                result = convertTemperature(value, fromUnitKey, toUnitKey);
            } else {
                result = convertStandard(value, fromUnitKey, toUnitKey);
            }

            if (isNaN(result) || !isFinite(result)) {
                elements.targetResult.textContent = 'Error';
                elements.detailedResult.textContent = 'Conversion error occurred.';
                return;
            }

            const fromUnitName = units[currentCategory][fromUnitKey].name;
            const toUnitName = units[currentCategory][toUnitKey].name;

            // Format result for display (max 6 decimal places, avoiding trailing zeros)
            const formattedResult = parseFloat(result.toFixed(6)).toLocaleString('en-US', {
                maximumFractionDigits: 6
            });
            const formattedValue = value.toLocaleString('en-US');

            elements.targetResult.textContent = formattedResult;
            
            // Update detailed result
            elements.detailedResult.textContent = 
                `${formattedValue} ${fromUnitName}${value === 1 ? '' : 's'} equals ${formattedResult} ${toUnitName}${result === 1 ? '' : 's'}`;
        }

        /**
         * Initializes event listeners.
         */
        function setupEventListeners() {
            elements.category.addEventListener('change', () => {
                currentCategory = elements.category.value;
                populateUnitDropdowns();
                performConversion();
            });

            // Attach performConversion to all relevant input/select changes
            elements.sourceValue.addEventListener('input', performConversion);
            elements.sourceUnit.addEventListener('change', performConversion);
            elements.targetUnit.addEventListener('change', performConversion);
        }

        /**
         * Initial setup when the window loads.
         */
        window.onload = function() {
            // 1. Setup UI
            populateUnitDropdowns();
            
            // 2. Setup Interactions
            setupEventListeners();

            // 3. Perform initial conversion
            performConversion();
        };

    </script>
</body>
</html>

