
## 1. As a button

As a training example: Nucleo-G474 with built-in LED, named in CubeIDE as LD2 (PA5) and button as B1 (PC13)


Put this inside of `while` loop:
```
if (HAL_GPIO_ReadPin(B1_GPIO_Port, B1_Pin)) {
     HAL_GPIO_WritePin(LD2_GPIO_Port, LD2_Pin, GPIO_PIN_SET);
   } else {
     HAL_GPIO_WritePin(LD2_GPIO_Port, LD2_Pin, GPIO_PIN_RESET);
   };
```

Which can be simplified down to:
```
HAL_GPIO_WritePin(LD2_GPIO_Port, LD2_Pin, HAL_GPIO_ReadPin(B1_GPIO_Port, B1_Pin));
```
And if we want to make LED lighting when button is not pressed:
```
HAL_GPIO_WritePin(LD2_GPIO_Port, LD2_Pin, !HAL_GPIO_ReadPin(B1_GPIO_Port, B1_Pin));
```

## 2. As a switch / latch


Define global user variable:
```
bool button_block;
```
and remember to include library to work with boolean variables:

```
#include 'stdbool.h'
```

Put this inside of `while` loop:
```
// if button is pressed and is not blocked
   if ((HAL_GPIO_ReadPin(B1_GPIO_Port, B1_Pin)) & !button_block){
     // block the further reading of button
     // to prevent further triggering by pressing
     button_block = 1;
     // toggle the LED
     HAL_GPIO_TogglePin(LD2_GPIO_Port, LD2_Pin);
     // if button is released
   } else if (!(HAL_GPIO_ReadPin(B1_GPIO_Port, B1_Pin)) & button_block) {
     // make release the block
     button_block = 0;
```
