<!DOCTYPE html> 

<html lang="en"> 

<head> 

    <meta charset="UTF-8"> 

    <meta name="viewport" content="width=device-width, initial-scale=1.0"> 

    <title>Shooting Game</title> 

    <style> 

        body { 

            margin: 0; 

            overflow: hidden; 

            background-color: black; 

            color: white; 

            font-family: Arial, sans-serif; 

        } 

        canvas { 

            display: block; 

        } 

    </style> 

</head> 

<body> 

    <script> 

        const canvas = document.createElement('canvas'); 

        document.body.appendChild(canvas); 

        const ctx = canvas.getContext('2d'); 

  

        canvas.width = window.innerWidth; 

        canvas.height = window.innerHeight; 

  

        const PLAYER_WIDTH = 50; 

        const PLAYER_HEIGHT = 20; 

        const BULLET_WIDTH = 5; 

        const BULLET_HEIGHT = 10; 

        const ENEMY_WIDTH = 40; 

        const ENEMY_HEIGHT = 20; 

        const ENEMY_BULLET_WIDTH = 5; 

        const ENEMY_BULLET_HEIGHT = 10; 

  

        const player = { 

            x: canvas.width / 2 - PLAYER_WIDTH / 2, 

            y: canvas.height - 60, 

            width: PLAYER_WIDTH, 

            height: PLAYER_HEIGHT, 

            lives: 3, 

            bullets: [] 

        }; 

  

        let enemies = []; 

        let enemyBullets = []; 

        let score = 0; 

        const enemiesPerWave = 5; 

        const totalEnemies = 20; 

        let currentWave = 0; 

        let gameOver = false; 

  

        function createWave() { 

            for (let i = 0; i < enemiesPerWave; i++) { 

                const x = i * (ENEMY_WIDTH + 20) + 50; 

                const y = 50 + currentWave * (ENEMY_HEIGHT + 30); 

                enemies.push({ x, y, width: ENEMY_WIDTH, height: ENEMY_HEIGHT }); 

            } 

        } 

  

        function drawPlayer() { 

            ctx.fillStyle = 'blue'; 

            ctx.fillRect(player.x, player.y, player.width, player.height); 

        } 

  

        function drawEnemies() { 

            ctx.fillStyle = 'red'; 

            enemies.forEach(enemy => { 

                ctx.fillRect(enemy.x, enemy.y, enemy.width, enemy.height); 

            }); 

        } 

  

        function drawBullets() { 

            ctx.fillStyle = 'yellow'; 

            player.bullets.forEach(bullet => { 

                ctx.fillRect(bullet.x, bullet.y, BULLET_WIDTH, BULLET_HEIGHT); 

            }); 

            ctx.fillStyle = 'orange'; 

            enemyBullets.forEach(bullet => { 

                ctx.fillRect(bullet.x, bullet.y, ENEMY_BULLET_WIDTH, ENEMY_BULLET_HEIGHT); 

            }); 

        } 

  

        function moveBullets() { 

            player.bullets.forEach((bullet, index) => { 

                bullet.y -= 5; 

                if (bullet.y + BULLET_HEIGHT < 0) { 

                    player.bullets.splice(index, 1); 

                } 

            }); 

            enemyBullets.forEach((bullet, index) => { 

                bullet.y += 5; 

                if (bullet.y > canvas.height) { 

                    enemyBullets.splice(index, 1); 

                } 

            }); 

        } 

  

        function detectCollisions() { 

            player.bullets.forEach((bullet, bIndex) => { 

                enemies.forEach((enemy, eIndex) => { 

                    if ( 

                        bullet.x < enemy.x + enemy.width && 

                        bullet.x + BULLET_WIDTH > enemy.x && 

                        bullet.y < enemy.y + enemy.height && 

                        bullet.y + BULLET_HEIGHT > enemy.y 

                    ) { 

                        player.bullets.splice(bIndex, 1); 

                        enemies.splice(eIndex, 1); 

                        score++; 

  

                        if (score % enemiesPerWave === 0 && score < totalEnemies) { 

                            currentWave++; 

                            createWave(); 

                        } 

                    } 

                }); 

            }); 

  

            enemyBullets.forEach((bullet, index) => { 

                if ( 

                    bullet.x < player.x + player.width && 

                    bullet.x + ENEMY_BULLET_WIDTH > player.x && 

                    bullet.y < player.y + player.height && 

                    bullet.y + ENEMY_BULLET_HEIGHT > player.y 

                ) { 

                    enemyBullets.splice(index, 1); 

                    player.lives--; 

  

                    if (player.lives <= 0) { 

                        gameOver = true; 

                    } 

                } 

            }); 

        } 

  

        function enemyShoot() { 

            enemies.forEach(enemy => { 

                if (Math.random() < 0.02) { 

                    enemyBullets.push({ 

                        x: enemy.x + enemy.width / 2 - ENEMY_BULLET_WIDTH / 2, 

                        y: enemy.y + ENEMY_HEIGHT, 

                        width: ENEMY_BULLET_WIDTH, 

                        height: ENEMY_BULLET_HEIGHT 

                    }); 

                } 

            }); 

        } 

  

        function drawUI() { 

            ctx.fillStyle = 'white'; 

            ctx.font = '20px Arial'; 

            ctx.fillText(`Lives: ${player.lives}`, 20, 30); 

            ctx.fillText(`Score: ${score}`, 20, 60); 

        } 

  

        function update() { 

            if (gameOver) { 

                ctx.fillStyle = 'white'; 

                ctx.font = '40px Arial'; 

                ctx.fillText('Game Over!', canvas.width / 2 - 100, canvas.height / 2); 

                return; 

            } 

  

            ctx.clearRect(0, 0, canvas.width, canvas.height); 

  

            drawPlayer(); 

            drawEnemies(); 

            drawBullets(); 

            drawUI(); 

  

            moveBullets(); 

            detectCollisions(); 

            enemyShoot(); 

  

            if (score >= totalEnemies) { 

                ctx.fillStyle = 'white'; 

                ctx.font = '40px Arial'; 

                ctx.fillText('You Win!', canvas.width / 2 - 100, canvas.height / 2); 

                gameOver = true; 

            } 

  

            requestAnimationFrame(update); 

        } 

  

        createWave(); 

  

        document.addEventListener('keydown', (e) => { 

            if (e.key === 'ArrowLeft' && player.x > 0) { 

                player.x -= 10; 

            } else if (e.key === 'ArrowRight' && player.x < canvas.width - player.width) { 

                player.x += 10; 

            } else if (e.key === ' ') { 

                player.bullets.push({ 

                    x: player.x + player.width / 2 - BULLET_WIDTH / 2, 

                    y: player.y, 

                    width: BULLET_WIDTH, 

                    height: BULLET_HEIGHT 

                }); 

            } 

        }); 

  

        update(); 

    </script> 

</body> 

</html> 
