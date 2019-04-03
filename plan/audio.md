# Audio-Ausgabe vom ESP32

* Das Projekt [ESP32-Radio](https://github.com/Edzelf/ESP32-Radio) benutzt den Chip [VS1053](https://www.sparkfun.com/datasheets/Components/SMD/vs1053.pdf) als MP3-Decoder. Der Micro-Controller muss die Daten via SPI von der SD-Karte in den Decoder übertragen. Der Decoder hat einen I2S- und Kopfhörer-Ausgang.
* Es ist möglich, MP3 direkt auf dem ESP32 zu dekodieren und dann via I2S auszugeben: 
  * https://github.com/pewit-tech/esp32-mp3-decoder basiert auf FreeRTOS als Basisbetriebssystem
  * https://github.com/MrBuddyCasino/ESP32_MP3_Decoder scheint auf der selben Grundlage zu basieren

Der ESP32 hat mehrere mögliche Ausgabe-Kanäle:
* [8-bit DAC](https://docs.espressif.com/projects/esp-idf/en/latest/api-reference/peripherals/dac.html): zwei Digital-Analog-Converter. Im DMA Modus können sie die Daten aus einem Puffer im Arbeitsspeicher lesen und mit festgelegter Abspielgeschwindigkeit ausgeben. Mit 8-Bit klingt es sicherlich ziemlich oldschool :)
* [PWM Puls-Weiten-Modulation](https://docs.espressif.com/projects/esp-idf/en/latest/api-reference/peripherals/ledc.html): Eigentlich gedacht, um LEDs anzusteuern. Der Zähler läuft mit maximal 40MHz. Dies ergibt ca. 19kHz Trägerfrequenz bei 11 bit Auflösung. Kaum besser als der DAC und kann kein DMA Modus.
* [Sigma-Delta-Modulation](https://docs.espressif.com/projects/esp-idf/en/latest/api-reference/peripherals/sigmadelta.html): Ähnlich zur Pulsweiten-Modulation, aber ohne feste Trägerfrequenz. Die Taktrate ist 80MHz und kann mit einem Teiler von 1 bis 255 geteilt werden, was mindestens 300kHz Trägerfrequenz entspricht. 8 Kanäle verfügbar, aber 8-bit und kein DMA.
* [I2S Inter-IC Sound](https://docs.espressif.com/projects/esp-idf/en/latest/api-reference/peripherals/i2s.html): ein digitaler Ausgang, um 16 bit Stereo Audio mit DMA Modus nach außen zu strömen zu lassen. Dahinter wird ein I2S Decoder Schaltkreis gebraucht, um es in ein analoges Audio-Signal umwandelt. Im Prinzip ein ausgelagerter DAC.

I2S Decoder:
* [NXP UDA1334A](https://www.nxp.com/pages/low-power-audio-dac-with-pll:UDA1334)
  * https://eckstein-shop.de/Adafruit-I2S-Stereo-Decoder-NXP-UDA1334A-Breakout
* [TI PCM5102A](http://www.ti.com/product/PCM5102A/description)
* [MAX98357A](https://www.maximintegrated.com/en/products/analog/audio/MAX98357A.html): mit integriertem 3W Verstärker für 3,3V Systeme
  * https://www.adafruit.com/product/3006 sowie https://www.sparkfun.com/products/14809
  * Footprint: [16-pin TQFN](https://www.analog.com/media/en/package-pcb-resources/package/pkg_pdf/ltc-legacy-qfn/QFN_16_05-08-1700.pdf) sehr kleines Gehäuse mit 0,5mm Pad-Abstand
  * Abwärme über Leiterplatte mit der Padfläche unter dem Chip: "Connect the exposed page to a solid ground plane for thermal dissipation." Sparkfun hat eine Durchkontaktierung unter dem Chip. Adafruit geht mit GND Fläche auf der Oberseite bis unter den Chip und hat 4 Durchkonaktierungen an den vier Ecken neben dem Chip.
  * Für 6 bis 8€ aus Großbritannien, oder von billigeren Fertigplatinen ablöten :)
* [ES9023](http://www.esstech.com/index.php/en/products/sabre-digital-analog-converters/sabre-hifi-mobile-dacs/sabre-hifi-stereo-integrated-dacs/es9023/)
  * Datenblatt: https://datasheetspdf.com/datasheet/ES9023.html von 2010

MP3 Decoder:
* [VS1053](https://www.sparkfun.com/datasheets/Components/SMD/vs1053.pdf)
  * den IC gibt es für 3 Euro via Ebay aus China
  * https://www.waveshare.com/wiki/Music_Shield
  * Footprint: [LPQFP-48](https://www.nxp.com/docs/en/package-information/SOT313-2.pdf) mit 0,5mm Pin-Abstand
