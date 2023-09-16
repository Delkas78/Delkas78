<!DOCTYPE html>
<html>
<head>
    <title>Mario Dino Game</title>
    <style>
        body {
            margin: 0;
            overflow: hidden;
        }

        #game-container {
            position: absolute;
            width: 100%;
            height: 100%;
            background-color: #87CEEB; /* Couleur de fond de ciel bleu clair */
        }

        #mario {
            position: absolute;
            width: 100px; /* Largeur de Mario augmentée */
            height: 150px; /* Hauteur de Mario augmentée */
            background-color: red;
            bottom: 0;
            left: 50px; /* Position de départ de Mario */
        }

        .obstacle {
            position: absolute;
            width: 100px; /* Largeur des obstacles augmentée */
            height: 100px; /* Hauteur des obstacles augmentée */
            background-color: green;
            bottom: 0;
        }
    </style>
</head>
<body>
    <div id="game-container">
        <div id="mario"></div>
    </div>
    
    <script>
        let isJumping = false;
        let jumpHeight = 500; // Hauteur de saut augmentée (pas de limite)
        let obstacleSpeed = 20; // Vitesse de départ des obstacles encore augmentée
        let accelerationInterval;
        const obstacleInterval = 4000; // Intervalle pour générer des obstacles (4 secondes)
        const maxObstacles = 2; // Limite de 2 obstacles à l'écran
        let obstacleQueue = []; // File d'attente pour les obstacles
        let consecutiveAvoided = 0; // Nombre d'obstacles évités consécutivement
        
        // Fonction pour faire avancer les obstacles
        function moveObstacles() {
            const obstacles = document.querySelectorAll(".obstacle");

            obstacles.forEach(function(obstacle) {
                let obstacleLeft = parseInt(getComputedStyle(obstacle).left, 10);
                obstacleLeft -= obstacleSpeed;
                obstacle.style.left = obstacleLeft + "px";

                // Si l'obstacle sort de l'écran, réinitialiser sa position à droite
                if (obstacleLeft < -100) {
                    obstacle.style.left = "100%"; // Position de départ à droite
                }

                // Vérifier la collision entre Mario et les obstacles
                const marioRect = mario.getBoundingClientRect();
                const obstacleRect = obstacle.getBoundingClientRect();

                if (
                    marioRect.bottom >= obstacleRect.top &&
                    marioRect.top <= obstacleRect.bottom &&
                    marioRect.right >= obstacleRect.left &&
                    marioRect.left <= obstacleRect.right
                ) {
                    // Collision détectée, réinitialiser le jeu
                    resetGame();
                }
            });
        }

        // Appeler la fonction moveObstacles toutes les 100 millisecondes pour faire avancer les obstacles
        setInterval(moveObstacles, 100);

        document.addEventListener("keydown", function(event) {
            if (event.key === "ArrowUp" && !isJumping) {
                isJumping = true;
                jump();
            } else if (event.key === "ArrowRight") {
                moveRight();
            } else if (event.key === "ArrowLeft") {
                moveLeft();
            } else if (event.key === "ArrowDown") {
                // Appeler la fonction pour faire sauter Mario vers le bas
                jumpDown();
            }
        });

        function jump() {
            let jumpDuration = 500; // 0.5 seconde (plus rapide)
            
            mario.style.transition = `bottom ${jumpDuration}ms ease-out`;
            mario.style.bottom = `${jumpHeight}px`; // Hauteur de saut augmentée (pas de limite)

            setTimeout(() => {
                mario.style.transition = `bottom ${jumpDuration}ms ease-in`;
                mario.style.bottom = "0";
                isJumping = false;
            }, jumpDuration);
        }

        function moveRight() {
            let marioLeft = parseInt(getComputedStyle(mario).left, 10);
            marioLeft += 20; // Déplacement vers la droite
            mario.style.left = marioLeft + "px";
        }

        function moveLeft() {
            let marioLeft = parseInt(getComputedStyle(mario).left, 10);
            marioLeft -= 20; // Déplacement vers la gauche
            mario.style.left = marioLeft + "px";
        }

        // Fonction pour faire sauter Mario vers le bas
        function jumpDown() {
            let jumpDuration = 500; // 0.5 seconde (plus rapide)

            mario.style.transition = `bottom ${jumpDuration}ms ease-out`;
            mario.style.bottom = "0"; // Saut vers le bas

            setTimeout(() => {
                mario.style.transition = `bottom ${jumpDuration}ms ease-in`;
                mario.style.bottom = "0";
            }, jumpDuration);
        }

        // Fonction pour accélérer le jeu toutes les 3 secondes jusqu'à 21 secondes
        function accelerateGame() {
            if (obstacleSpeed < 21) {
                obstacleSpeed += 1;
            }
        }

        // Appeler la fonction accelerateGame toutes les 3 secondes
        accelerationInterval = setInterval(accelerateGame, 3000);

        // Fonction pour générer un obstacle aléatoire
        function generateRandomObstacle() {
            if (obstacleQueue.length < maxObstacles) {
                const obstacle = document.createElement("div");
                obstacle.className = "obstacle";
                obstacle.style.left = "100%"; // Position de départ à droite
                const randomHeight = Math.floor(Math.random() * 200) + 50; // Hauteur aléatoire
                obstacle.style.height = randomHeight + "px";
                document.getElementById("game-container").appendChild(obstacle);
                obstacleQueue.push(obstacle);
            }
        }

        // Appeler la fonction generateRandomObstacle à intervalles pour générer des obstacles
        setInterval(generateRandomObstacle, obstacleInterval);

        // Fonction pour réinitialiser le jeu
        function resetGame() {
            // Supprimer tous les obstacles
            const obstacles = document.querySelectorAll(".obstacle");
            obstacles.forEach(function(obstacle) {
                obstacle.remove();
            });

            // Réinitialiser la vitesse des obstacles, la position de Mario et la file d'attente des obstacles
            obstacleSpeed = 20;
            mario.style.bottom = "0";
            obstacleQueue = [];
            consecutiveAvoided = 0;
        }
    </script>
</body>
</html>
  
