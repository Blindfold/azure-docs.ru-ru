<!-- F-series, Fs-series* -->

Серия F работает на базе процессора Intel Xeon® E5-2673 вер. 3 (Haswell) с тактовой частотой 2,4 ГГц, ускоряемого до 3,1 ГГц с помощью технологии Intel Turbo Boost Technology 2.0. Такую же производительность ЦП обеспечивает серия виртуальных машин Dv2.  Предлагая более низкую ориентировочную стоимость часа, серия F обеспечивает наилучшее соотношение цены и производительности в портфеле Azure в единицах вычисления Azure (ACU) на ядро. 

Виртуальные машины серии F идеально подходят для рабочих нагрузок, которым требуются более быстрые процессоры, но не требуется много памяти или локальной емкости SSD на ядро ЦП.  Преимущества серии F нацелены на такие рабочие нагрузки, как обработка аналитики, игровые серверы, веб-серверы и пакетная обработка.

Серия Fs включает все преимущества серии F ряда, включая возможность использовать хранилище класса Premium.

## <a name="fs-series"></a>Серия Fs*

ACU: 210–250

| Размер | Ядра ЦП | Память, ГиБ | Локальный SSD: ГиБ | Максимальное число дисков данных | Максимальная пропускная способность дисков с кэшированием и локальных дисков: операций ввода-вывода в секунду / Мбит/с (размер кэша указан в ГиБ) | Максимальная пропускная способность дисков без кэширования, операций ввода-вывода в секунду / Мбит/с | Максимальное число сетевых карт, максимальная пропускная способность сети |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_F1s |1 |2 |4. |2 |4000 / 32 (12) |3200 / 48 |2, средняя |
| Standard_F2s |2 |4. |8 |4. |8000 / 64 (24) |6400 / 96 |2, высокая |
| Standard_F4s |4. |8 |16 |8 |16 000 / 128 (48) |12 800 / 192 |4, высокая |
| Standard_F8s |8 |16 |32 |16 |32 000 / 256 (96) |25 600 / 384 |8, высокая |
| Standard_F16s |16 |32 |64 |32 |64 000 / 512 (192) |51 200 / 768 |8, чрезвычайно высокая |

1 Мбит/с = 10^6 байтов в секунду, а 1 ГиБ = 1024^3 байтов.

* Максимальная возможная пропускная способность дисков (в операциях ввода-вывода в секунду или Мбит/с) виртуальных машин серии Fs может быть ограничена из-за количества, размера и чередования подключенных дисков.  Дополнительные сведения см. в разделе [Хранилище класса "Премиум": высокопроизводительная служба хранилища для рабочих нагрузок виртуальных машин Azure](../articles/storage/storage-premium-storage.md).

<br>
## <a name="f-series"></a>Серия F

ACU: 210–250

| Размер         | Ядра ЦП | Память, ГиБ | Локальный SSD: ГиБ | Макс. пропускная способность локального диска: операций ввода-вывода в секунду / чтение Мбит/с / запись Мбит/с | Макс. число дисков данных / пропускная способность: операций ввода-вывода в секунду | Максимальное число сетевых карт, максимальная пропускная способность сети |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_F1  | 1         | 2           | 16             | 3000 / 46 / 23                                           | 2 / 2x500                         | 2, средняя                 |
| Standard_F2  | 2         | 4.           | 32             | 6000 / 93 / 46                                           | 4 / 4x500                         | 2, высокая                     |
| Standard_F4  | 4.         | 8           | 64             | 12000 / 187 / 93                                         | 8 / 8x500                         | 4, высокая                     |
| Standard_F8  | 8         | 16          | 128            | 24000 / 375 / 187                                        | 16 / 16x500                       | 8, высокая                     |
| Standard_F16 | 16        | 32          | 256            | 48000 / 750 / 375                                        | 32 / 32x500                       | 8, чрезвычайно высокая           |
<br>


