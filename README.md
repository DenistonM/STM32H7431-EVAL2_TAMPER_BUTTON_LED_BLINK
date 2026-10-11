# STM32H7431-EVAL2_TAMPER_BUTTON_LED_BLINK
Controlling an LED using the tamper button on the STM32H743I-EVAL2 development board.

Tamper button and LED: steps and code
This is the setup that worked on your board, with the code explained.

What the hardware does
- Tamper button: pin PC13, label TAMPER. I set it as an input with pull-up, so it reads High when released and Low when pressed.
- LED1 (green): pin PF10, label LED1_RGB. I set it as a push-pull output. The LED turns on when the pin is High.
- 
Steps in CubeMX
1. File → New Project → Board Selector → STM32H743I-EVAL2, then click Start Project. Answer Yes to default initialization.
2. In System Core → GPIO, check the two pins:
   - TAMPER (PC13): mode GPIO_Input, Pull-up.
   - LED1_RGB [LD1_Green] (PF10): mode Output Push Pull, no pull, initial level Low.
3. Check that RTC has no green tick, so it doesn’t take over PC13.
4. In Project Manager, set the name Tamper_Test, choose a short folder path, set Toolchain / IDE to STM32CubeIDE, and click GENERATE CODE.
5. In CubeIDE, use File → Import → STM32CubeMX/STM32CubeIDE Project and select the project folder.
6. Open Core → Inc → main.h to find the pin names. They are TAMPER_Pin, TAMPER_GPIO_Port, LED1_RGB_Pin and LED1_RGB_GPIO_Port.
The code (in Core/Src/main.c)
Add these two lines in the USER CODE BEGIN PD section:

c
/* USER CODE BEGIN PD */
#define BUTTON_PRESSED_LEVEL  GPIO_PIN_RESET   /* pressed = Low (pull-up) */
#define LED_ON_LEVEL          GPIO_PIN_SET     /* LED on = High */
/* USER CODE END PD */
Replace the main loop with this. Each marker must appear only once:

c
  /* USER CODE BEGIN WHILE */
  while (1)
  {
    /* USER CODE END WHILE */

    /* USER CODE BEGIN 3 */
    if (HAL_GPIO_ReadPin(TAMPER_GPIO_Port, TAMPER_Pin) == BUTTON_PRESSED_LEVEL)
    {
      HAL_GPIO_WritePin(LED1_RGB_GPIO_Port, LED1_RGB_Pin, LED_ON_LEVEL);
    }
    else
    {
      HAL_GPIO_WritePin(LED1_RGB_GPIO_Port, LED1_RGB_Pin,
                        (LED_ON_LEVEL == GPIO_PIN_SET) ? GPIO_PIN_RESET : GPIO_PIN_SET);
    }
  }
  /* USER CODE END 3 */
}

How the code works

- HAL_GPIO_ReadPin(TAMPER_GPIO_Port, TAMPER_Pin) reads the button pin. With the pull-up, an idle pin reads High, and pressing the button connects it to ground, so it reads Low.
- The if compares that reading with BUTTON_PRESSED_LEVEL. When they match, the button is pressed.
- HAL_GPIO_WritePin(..., LED_ON_LEVEL) drives the LED pin High, which turns the LED on.
- The else branch writes the opposite level, so the LED turns off when you release the button.
- The loop repeats forever, so the LED follows the button continuously.
If the LED ever behaves the opposite way, swap the two #define values.
Build, flash and test
1. Press Ctrl+S, then click the hammer icon. You want 0 errors.
2. Connect the ST-LINK USB, click the bug icon (Debug), and click Resume (F8).
3. Press and hold the Tamper button. LED1 turns off while it is held and on when released.
