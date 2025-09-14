# UART Basic

主要是使用串口过程中的一些经验总结

## STM32CubeMX

配置主要是中断要记得打开，DMA要配置


## Frame Header and End

主要是之前项目中使用过的用于保证通讯传输准确性的校验


## IDLE Receive(with DMA)
```c

HAL_UARTEx_ReceiveToIdle_IT(&huart3, rxdata_u3,50 );
HAL_UARTEx_ReceiveToIdle_DMA(&huart4, rxdata_u4, 40);

void HAL_UARTEx_RxEventCallback(UART_HandleTypeDef *huart, uint16_t Size)
{
    if(huart->Instance == USART3)
    {
        HAL_UARTEx_ReceiveToIdle_IT(huart, rxdata_u3, 40);
        is_raspi_get_massage = 1;
        UART_handle_function_3(); // don't do too many things in this


        rxflag_u3 = 0;
        memset(rxdata_u3, 0, 50);
    }
    else if(huart->Instance == UART4)
    {
        gyro_data_process(); // don't do too many things in this
        
        rxflag_u4 = 0;
        memset(rxdata_u4, 0, 40);
        
        // 重新启动DMA接收
        HAL_UARTEx_ReceiveToIdle_DMA(&huart4, rxdata_u4, 40);
    }
}



```



## Simple Receive(without any prevention)


```c
HAL_UART_Receive_IT(&huart1, &received_rxdata_u1, 1);

void HAL_UART_RxCpltCallback(UART_HandleTypeDef *huart)
{
    if(rxflag_u1 >= 128)
    {
        rxflag_u1 = 0;
    }
    rxdata_u1[rxflag_u1++] = received_rxdata_u1;
    if(huart->Instance == USART1)
    {
        HAL_UART_Receive_IT(huart, &received_rxdata_u1, 1); // 每次处理一个字符
    }
}


void UART_handle_function_1(void)
{
    if(rxflag_u1 != 0)
    {
        int temp = rxflag_u1;
        // HAL_Delay(1); // 如果是在main中使用可以加入延时，在定时器中断中调用则不能加
        if(temp == rxflag_u1) 
        {
            UART_receive_process_1(); //在这个函数里对数据进行处理
        }
    }
}

```