<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CatGPT: The Purrfect Answers</title>
    
    <script src="https://cdn.tailwindcss.com">
        tailwind.config = {
            theme: {
                extend: {},
            }
        }
    </script>

    <style>
        /* Use a nice, warm background color for the whole page */
        body {
            background-color: #a53f2b; /* Chestnut color */
            font-family: 'Inter', sans-serif;
        }
        /* Make the image responsive and max out at the container width */
        #cat-image {
            max-width: 100%;
            height: auto;
            border-radius: 1rem; /* Rounded corners for the picture */
            box-shadow: 0 10px 15px rgba(0, 0, 0, 0.1);
            min-height: 300px;
            object-fit: cover;
        }
        /* A simple pulse animation for the "thinking" state */
        @keyframes pulse {
          0%, 100% {
            opacity: 1;
          }
          50% {
            opacity: .5;
          }
        }
        .animate-pulse {
          animation: pulse 2s cubic-bezier(0.4, 0, 0.6, 1) infinite;
        }
    </style>
</head>
<body class="p-4 sm:p-8 flex items-center justify-center min-h-screen">

    <div class="container mx-auto max-w-2xl bg-white p-6 sm:p-10 rounded-3xl shadow-2xl border-4 border-pink-200">

        <h1 class="text-4xl sm:text-5xl font-extrabold text-pink-600 mb-2 flex items-center">
            <span class="text-amber-700">CatGPT</span> 🐾
        </h1>
        <p class="text-lg text-gray-600 mb-6">
            Meow can I help you today?
        </p>

        <div class="flex flex-col sm:flex-row gap-4 mb-8">
            <input 
                type="text" 
                id="prompt-input" 
                placeholder="Meow" 
                class="flex-grow p-4 border-2 border-pink-300 rounded-xl focus:outline-none focus:ring-4 focus:ring-pink-400/50"
            />
            <button 
                id="submit-button" 
                class="py-4 px-8 bg-pink-500 hover:bg-pink-600 text-white font-bold rounded-xl shadow-lg transition duration-200 disabled:opacity-50 disabled:cursor-not-allowed transform hover:scale-[1.01]"
            >
                Pawsible Answer
            </button>
        </div>

        <div id="response-area" class="bg-gray-100 p-2 rounded-xl transition-all duration-300">
            <img 
                id="cat-image" 
                src="https://placehold.co/400x300/fecaca/92400e?text=Waiting+for+CatGPT" 
                alt="CatGPT Response Image" 
                class="w-full"
            />
        </div>

    </div>

    <script>
        // The address for the Cat Picture Warehouse (The Cat API)
        const CAT_API_URL = 'https://api.thecatapi.com/v1/images/search';
        // ⚠️ YOU MUST REPLACE 'YOUR_API_KEY_HERE' WITH YOUR ACTUAL KEY
        const API_KEY = 'live_c7AxKrS0bcCT6MzYyAahHMoCmKGqPL8GEd3TfQRLLFjoaAfHWzxAF3KTAym0Rjn4'; 

        /**
         * This function is the "brain" of CatGPT. 
         * It handles the click, calls the API, and updates the image.
         */
        async function fetchCatPicture() {
            // Find the elements on the page using their IDs
            const submitButton = document.getElementById('submit-button');
            const catImageElement = document.getElementById('cat-image');
            const responseArea = document.getElementById('response-area');
            const promptInput = document.getElementById('prompt-input');

            // --- 1. Show the "Thinking" State ---
            catImageElement.src = "https://placehold.co/400x300/f87171/FFFFFF?text=CatGPT+is+Thinking...";
            responseArea.classList.add('animate-pulse');
            submitButton.disabled = true; 
            promptInput.value = ''; 

            try {
                // --- 2. Call the API (The actual "logic") ---
                
                // FIX: Create request options to include the API Key header
                const requestOptions = {
                    method: 'GET',
                    headers: {
                        'x-api-key': API_KEY 
                    }
                };

                // Fetch the cat image using the options
                const response = await fetch(CAT_API_URL, requestOptions);

                if (!response.ok) {
                    throw new Error(`API call failed with status: ${response.status}`);
                }

                // Turn the internet reply into something JavaScript can read (JSON)
                const data = await response.json();
                
                // The Cat API gives back a list, so we take the URL from the first item
                const imageUrl = data[0]?.url;

                // --- 3. Display the Cat ---
                if (imageUrl) {
                    // Create a temporary image to make sure the picture is loaded fully before we show it.
                    const tempImg = new Image();
                    tempImg.onload = () => {
                        catImageElement.src = imageUrl;
                    };
                    tempImg.onerror = () => {
                        catImageElement.src = "https://placehold.co/400x300/ef4444/FFFFFF?text=ERROR%3A+Cannot+Load+Fuzzy+Image";
                    };
                    tempImg.src = imageUrl; 
                } else {
                    catImageElement.src = "https://placehold.co/400x300/ef4444/FFFFFF?text=ERROR%3A+No+Cat+Found+In+Warehouse";
                }

            } catch (error) {
                console.error('CatGPT Error:', error);
                catImageElement.src = "https://placehold.co/400x300/ef4444/FFFFFF?text=NETWORK+ERROR";
            } finally {
                // --- 4. Restore the App ---
                responseArea.classList.remove('animate-pulse');
                submitButton.disabled = false;
            }
        }

        // --- 5. Initial Setup ---
        window.onload = function() {
            const submitButton = document.getElementById('submit-button');
            
            // Tell the button: "When you are clicked, run fetchCatPicture!"
            submitButton.addEventListener('click', fetchCatPicture);
            
            // Get the very first cat picture ready when the app starts
            fetchCatPicture();
        }
    </script>
</body>
</html>
