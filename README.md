# opencv-hsv-problem

Struggling to detect colors with OpenCV in the HSV color space? You're not alone

## TL;DR

Ao invés de inserir números escalares para os limites do intervalo da cor no método inRange do OpenCV, insira uma equação aritmética com a tolerância, como ilustrado abaixo e em 01-Notebook.ipynb, e o método por si só irá unir os intervalos descontínuos.

```
❌ cv2.inRange(
    imgHSV,
    np.array([-20, 255, 255]),
    np.array([20, 255, 255])
   )

✅ cv2.inRange(
    imgHSV,
    np.array([angle_180 - hueTolerance, 255, 255]),
    np.array([angle_180 + hueTolerance, 255, 255])
   )
```

## Explicação

Note que no espaço de cores HSV, H ∈ [0°, 360°], como ilustrado no círculo cromático em 03-HSV_360.png

Contudo, no OpenCV, H ∈ [0°, 180°], como ilustrado no círculo cromático em 03-HSV_180.png

Apenas dividir o ângulo obtido no círculo cromático 03-HSV_360 e usar no OpenCV até que funciona em alguns casos, porém há um problema:

O método inRange do OpenCV não aceita números negativos e os limites do intevalo desse método devem ser números escalares, e não outros intervalos.

Ou seja, apenas dividir o ângulo de 03-HSV_360 por dois não funciona para a cor vermelha, que está compreendida, segundo o 04-HSV_180.png e considerando uma tolerância de 20°, no intervalo [0°, 20°] ∪ [160°, 180°], pois independentemente do ângulo escolhido para essa cor, apenas metade do intervalo será detectado:

- Fórmula Empírica de Dividir o Ângulo por Dois para gerar os Limites do Intervalo (Não funciona para a cor vermelha)

```
intervalo_180(lim_360) = (lim_360 / 2) ± tol; lim={0°, 360°}; tol=20°
```

- Se lim_360=0°

```
intervalo_180(0°) = [(0°/2) - 20°, (0°/2) + 20°]
intervalo_180(0°) = [-20°, 20°]
intervalo_180(0°) = [0°, 20°] <-- Metade do Intervalo
```

- Se lim_360=360°

```
intervalo_180(360°) = [(360°/2) - 20°, (360°/2) + 20°]
intervalo_180(360°) = [180° - 20°, 180° + 20°]
intervalo_180(360°) = [160°, 180°] <-- Outra metade do Intervalo
```

A função do OpenCV não entende o que não está entre [0°, 180°], por isso a os limites ofensores são aproximados para o valor de limite de intervalo mais próximo para que a condição H ∈ [0°, 180°] seja satisfeita.

## TODO

- Ilustrar explicação do README.md
- Créditos para as imagens PNG
- MRE do erro
- Quickstart para executar o código com o Docker