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
linux에서 gpioinfo를 실행하면 gpio쪽 정보를 알수 있다
이정보를 바탕으로 gpiod.h 를 include 시켜서 라이브러리를 사용하여 gpio를 컨트롤 가능하다
libgpiod 사용 (강력 추천)
호스트에서는 아래 프로그램 설치 해야함
sudo apt install libgpiod-dev
타겟시스템에서는 아래 프로그램 make menuconfig 실행해서 추가해줌
→ Target packages

    → Libraries

        → Hardware handling
qt에서는 아래 항목 pro에 추가해줌
CONFIG += link_pkgconfig
PKGCONFIG += libgpiod
이부분 안해주면 qt에서 gpio.h를 못찾음

테스트 코드
#include <gpiod.h>
#include <iostream>

int main() {
    gpiod_chip *chip = gpiod_chip_open_by_name("gpiochip0");
    if (!chip) {
        std::cerr << "Failed to open gpiochip0\n";
        return 1;
    }

    gpiod_line *line = gpiod_chip_get_line(chip, 24);
    if (!line) {
        std::cerr << "Failed to get GPIO line\n";
        return 1;
    }

    gpiod_line_request_output(line, "qt-test", 1);
    gpiod_line_set_value(line, 1); // HIGH

    gpiod_line_release(line);
    gpiod_chip_close(chip);

    return 0;
}

C++버전 테스트 코드
#include <gpiod.hpp> // C++ API

gpiod::chip chip("gpiochip0");
gpiod::line line = chip.get_line(24);

line.request({
    "qt_app",
    gpiod::line_request::DIRECTION_OUTPUT,
    0 // 초기 값
});

line.set_value(1); // HIGH
