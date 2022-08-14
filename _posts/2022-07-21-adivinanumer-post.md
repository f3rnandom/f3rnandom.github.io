---
layout: post
title: "Adivina el número"
---


[Adivina el Numero][Github-f3rnandom] es la creación de un simple juego escrito en javaScript
en el cual debemos elgir los números secretos generados de forma aleatoria.

Dentro del desarollo podemos observar el uso de  `funciones`, así mismo, la implementación de funciones de manera correcta y encontrar la `sinergia` correcta entre los lenguajes de programación  `HTML` y `JAVASCRIPT` .


```html
<meta charset="UTF-8">

<!-- Espera a un dato sea ingresado -->
<input/>
<!-- Boton -->
<button>Verificar su acierto</button>

<script>

    // var secreto = Math.round(Math.random()*10);
    // Guarda la informacion ingresada y la guardad en la variable input
    
    function aleatorio(){

        return Math.round(Math.random()*10);

    }

    // var secretos = [];  //definicion de arreeglo
    // Funcion push agrega valores al arreglo
    // secretos.push();    

    function sortearNumeros(cantidad){

        var secretos = [];
        var contador = 1;

        while (contador <= cantidad){

            var numeroAleatorio = aleatorio();
            console.log(numeroAleatorio);
            var encontrado = false;
            //evitar numeros repetidos
            if (numeroAleatorio != 0){

                for (posicion = 0; posicion < secretos.length; posicion++) {
                    
                    if (numeroAleatorio == secretos[posicion]){
                        
                        encontrado = true;
                        break;
                    }
                }
                
                if(encontrado == false){
                    
                    secretos.push(numeroAleatorio);
                    contador++;
                }
            }
        }
        return secretos;
    }

    var secretos = sortearNumeros(4);

    console.log(secretos);
        
    var input = document.querySelector("input");
    input.focus();
    
    function verificar(){

        var encontrado = false;

        for(var posicion = 0; posicion < secretos.length; posicion++){
            //convierte el valor input en entero
            if (parseInt(input.value) == secretos[posicion]){
                
                alert("Usted Acerto!");
                encontrado = true;
                break;
            }

    }

    if(encontrado == false){
        alert("Usted no Acerto!");
    }
        
        input.value = ""; //limpia el cuadro de texto
        input.focus(); //coloca el cursosr en el cuadro
    }


    var button = document.querySelector("button");
    // a la escucha de la accion del boton
    button.onclick = verificar;
</script>
```

Ver código en [Github][Github-f3rnandom] para probar y opinar.

[Github-f3rnandom]: https://github.com/F3rnandom
