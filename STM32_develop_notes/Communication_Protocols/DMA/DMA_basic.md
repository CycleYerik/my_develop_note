# DMA Basic

这是平时DMA使用的总结



## UART + DMA 常用模式

### 1. 发送 (阻塞等待)
```c
HAL_UART_Transmit_DMA(&huart1, tx_data, len);
while(huart1.gState != HAL_UART_STATE_READY); // 等待发送完成
```

### 2. 接收 (IDLE中断)
```c
// 初始化
HAL_UARTEx_ReceiveToIdle_DMA(&huart1, rx_buffer, BUFFER_SIZE);

// 回调处理
void HAL_UARTEx_RxEventCallback(UART_HandleTypeDef *huart, uint16_t Size)
{
    if(huart->Instance == USART1) {
        // 处理接收到的Size字节数据
        process_received_data(rx_buffer, Size);
        
        // 重启接收
        HAL_UARTEx_ReceiveToIdle_DMA(huart, rx_buffer, BUFFER_SIZE);
    }
}
```

## SPI + DMA

### 双缓冲发送
```c
uint8_t spi_tx_buf1[256], spi_tx_buf2[256];
uint8_t current_buf = 0;

void send_spi_data(uint8_t* data, uint16_t len) {
    uint8_t* buf = current_buf ? spi_tx_buf2 : spi_tx_buf1;
    memcpy(buf, data, len);
    
    HAL_SPI_Transmit_DMA(&hspi1, buf, len);
    current_buf = !current_buf; // 切换缓冲区
}
```

## ADC + DMA (连续转换)

```c
uint16_t adc_buffer[8]; // 8个通道

// 启动连续转换
HAL_ADC_Start_DMA(&hadc1, (uint32_t*)adc_buffer, 8);

// 转换完成回调
void HAL_ADC_ConvCpltCallback(ADC_HandleTypeDef* hadc) {
    // adc_buffer中包含所有通道的最新值
    float voltage_ch0 = adc_buffer[0] * 3.3f / 4096.0f;
}
```

## 常见问题与解决





### 中断优先级
```c
// DMA中断优先级应低于数据产生中断
// 例如：定时器中断优先级 < DMA中断优先级
HAL_NVIC_SetPriority(TIM2_IRQn, 1, 0);        // 数据产生
HAL_NVIC_SetPriority(DMA1_Stream0_IRQn, 2, 0); // DMA传输
```

## 调试技巧

### 1. 检查DMA状态
```c
void check_dma_status() {
    if(__HAL_DMA_GET_FLAG(huart1.hdmatx, DMA_FLAG_TE1_5)) {
        // 传输错误
        __HAL_DMA_CLEAR_FLAG(huart1.hdmatx, DMA_FLAG_TE1_5);
    }
}
```


