# gui_gpio
gui gpio
디바이스트리:
        gpio_pf10: gpio-pf10 {
                pinmux = <STM32_PINMUX('F', 10, GPIO)>;
                bias-pull-up;
                drive-push-pull;
                slew-rate = <0>;
                status = "okay";
        };
