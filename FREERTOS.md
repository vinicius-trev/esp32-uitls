# Fundamentals in FreeRTOS

1. Tip FreeRTOS Learning Guide -> Start at chapter #3
2. x types in FreeRTOS return something
3. FreeRTOS Decision Tree:
    - Communicate with another task or send simple data -> Task Notification
    - Unblock or block a task -> Semaphore
    - Lock shared resource -> Mutex (the task that lock is the one that unlock)
    - Send complex data -> Queue
    - Multiple things before unblocking task -> Event Group
4. Task Notification
    - xTaskNotifyGive() ulTaskNotifyTake()
        - x return something
        - ul return unsigned long
    - Parameters: BaseType_t bool, TaskHandle_t task_handle (give), TickType_t delay (take)
        - BaseType_t -> pdFalse: The take funcion will count the number of given notifications and execute that number of times
        - BaseType_t -> pdTrue: The take function will reset the number of given notifications and execute only once (returns the number of times fired internally)
    - xTaskNotify(recv_handler, snd_state_bits, bit_set_flag) xTaskNotifyWait(clear_entry_mask, clear_exit_mask, rcv_state_bits, delay), where:
        - snd_state_bits -> Bitmask to set a state
        - bit_set_flag:
            - eNoAction -> Do not set the state bit
            - eSetBits -> Set the bitmask in the state bit
            - eIncrement -> Increment the state bit by one
            - eSetValueWithOverwrite -> Set the state bit overwriting the previous state value
            - eSetValueWithoutOverwrite -> Set the state bit without overwriting the previous state value
        - clear_entry_mask -> Clear notification bits when received the notification
        - clear_exit_mask -> Clear notification bits when exiting the notification
        - rcv_state_bits -> State bits received from the sender
        - delay -> Time delay in ticks
5. Use underline prefix before guard header _COMPONENT_H
6. Event Groups
    - Used to check two or more contitions before unlocking a task (similar as stacked semaphores between if statements)
    - One bit can be set twice without any error while waiting for another bit to unlock a task, just check the code logic
    - Included with freertos/event_groups.h
    - Use flags/bits to set the event type
    - Type: EventGroupHandle_t
    - Initialized with: xEventGroupCreate()
    - Set a new bit/event with: xEventGroupSetBits(handle, bit_to_set)
    - Monitor de setted bits with: xEventGroupWaitBits(handle, compare_bit_mask, clear_bits, wait_for_all_bits, timeout)
7. FreeRTOS Timers
    - xTimerCreate("timer_name", ticks_to_kick_timer, only_once_flag, timer_id, timer_callback) -> Return timer_handle TimerHandle_t
    - To start the timer: xTimerStart(timer_handle, time_to_wait_before_start)
    - These are software timers, called by the idle task. In other words do not do complex workload or delays inside the callback, need to be unblocking function without a lot of resources
    - The resolution is only 1ms, the callback (idle) task is a low priority task
8. High Resolution Timer
    - Include: esp_timer.h
    - To create a timer: esp_timer_create(timer_create_args, timer_handle)
    - To start once: esp_timer_start_once(timer_handle, timeout) -> Minimal timer resolution 20us to dispatch the callback
    - To start periodically: esp_timer_start_periodic(timer_handle, timeout) -> Minimal timer resolution 50us to dispatch the callback
    - Debug the timers: esp_timer_dump(file_stream/stdout)
