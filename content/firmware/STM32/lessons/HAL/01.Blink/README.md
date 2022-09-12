
As a training example: Nucleo-G474 with built-in LED, named in CubeIDE as LD2 (PA5) and button as B1 (PC13)

# 1. Simple way

- Ignite the led
- wait certain interval
- Exhaust the led
- ait certain time interval



place this structure in `while` loop:
```
// sets the pin high
HAL_GPIO_WritePin(LD2_GPIO_Port, LD2_Pin, GPIO_PIN_SET);
// do nothing for 200 ms
HAL_Delay(200);
// set the pin low
HAL_GPIO_WritePin(LD2_GPIO_Port, LD2_Pin, GPIO_PIN_RESET);
// do nothing for 200 ms
HAL_Delay(200);
```

# 2. Toggling + variable

Declare the variable of delay between:
```
/* USER CODE BEGIN PFP */

/* USER CODE END PFP */
```

as :
```
int delay_time = 200;
// or
// #define delay_time 200
```

place this structure in `while` loop:
```
// toggle the state of pin
HAL_GPIO_TogglePin(LD2_GPIO_Port, LD2_Pin);
// do nothing for defined time
HAL_Delay(200);
```

# 3. Toggling without delay

While code above is working, it is not recommended to use delay functions as during this time MCU doesn't do anything and reacts to nothing.
It might cause a sever problems in final device. Thus, it is better to calculate how much time passed since the last change and if needed, change the state.

Declare the variable of delay between:
```
/* USER CODE BEGIN PFP */

/* USER CODE END PFP */
```

as :
```
int delay_time = 200;
unsigned long last_tick;
```

Place this code before `while` loop:
```
// This will provide the timepoint which will be used later to compare time with
last_tick = HAL_GetTick();
```

I placed it between:
```
/* USER CODE BEGIN 2 */

/* USER CODE END 2 */
```


place this structure in `while` loop:
```
// if more time passed than
if (HAL_GetTick() - last_tick > delay_time){
  HAL_GPIO_TogglePin(LD2_GPIO_Port, LD2_Pin);
  last_tick = HAL_GetTick();
};
```

Alternatively, use :
```
// if more time passed than
unsigned long current_tick =  HAL_GetTick();
if (current_tick - last_tick > delay_time){
  HAL_GPIO_TogglePin(LD2_GPIO_Port, LD2_Pin);
  last_tick = current_tick;
};
```

# 4. Toggling without delay + Boolean flags

In case if there is something, requiring actions depending on the current state of LED, it would be reasonable to use boolean flags.
To  make example a bit more complicated, lets consider the different ON and OFF periods.

Place this:
```
int on_time = 100;
int off_time = 500;
bool led_state;
unsigned long last_tick;
```
Between following lines:
```
/* USER CODE BEGIN PFP */

/* USER CODE END PFP */
```


Than, add following code:
```
led_state = 1;
HAL_GPIO_WritePin(LD2_GPIO_Port, LD2_Pin, led_state);
last_tick = HAL_GetTick();
```
between lines:

```
/* USER CODE BEGIN 2 */

/* USER CODE END 2 */
```

Place the following code to `while` loop:
```
// if LED is OFF
if (led_state == 0) {
  // and it is time to turn it ON
  if (HAL_GetTick() - last_tick > off_time) {
    // Change the state of flag
    led_state = 1;
    last_tick = HAL_GetTick();
    HAL_GPIO_WritePin(LD2_GPIO_Port, LD2_Pin, led_state);
  };
};
// if LED is ON
if (led_state == 1) {
  // and it is time to toggle it
  if (HAL_GetTick() - last_tick > on_time) {
    led_state = 0;
    last_tick = HAL_GetTick();
    HAL_GPIO_WritePin(LD2_GPIO_Port, LD2_Pin, led_state);
  };
};
```

Since the bool variable can be only 0 or 1, than part:
```
};
// if LED is OFF
if (led_state == 1) {
```
can be replaced with simple :
```
} else {
```

However, it might be better to replace the `whole` loop with:

```
unsigned long current_tick = HAL_GetTick();
if (((led_state == 1) & (current_tick - last_tick > on_time))
    | ((led_state == 0) & (current_tick - last_tick > off_time))) {
  led_state = !led_state;
  last_tick = current_tick;
  HAL_GPIO_WritePin(LD2_GPIO_Port, LD2_Pin, led_state);
};
```
