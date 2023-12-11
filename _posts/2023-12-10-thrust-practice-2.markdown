---
layout: post
title:  "🌈 CUDA 프로그래밍: Thrust 실습 2편 (Feat. godbolt)"
date:   2023-12-10 23:00:00 +0000
categories: SLAM
---

# Compiler Explorer (godbolt) 에서 편하게 연습하자
- 난 CUDA 및 [Thrust 공부를 빠르고 편하게 + 어디에서나](https://godbolt.org/z/jMYs16nGq) 하고싶지 
- 빌드환경을 구축하는 건 귀찮다...
- 라는 마음이 들 때

## 티저
- ... 라는 마음이 들 때
- 아래처럼 실습을 하면 된다.
    <iframe width="1100px" height="700px" src="https://godbolt.org/e?readOnly=true#z:OYLghAFBqd5QCxAYwPYBMCmBRdBLAF1QCcAaPECAMzwBtMA7AQwFtMQByARg9KtQYEAysib0QXACx8BBAKoBnTAAUAHpwAMvAFYTStJg1DIAruiakl9ZATwDKjdAGFUtEywZ7HAGTwNMAHLuAEaYxCAArKQADqgKhHYMLm4eerHxtgK%2B/kEsoeFRVpg2iUIETMQEye6eXJaY1pkMZRUE2YEhYZGW5ZXVqXUKvW1%2BHXldEQCUlqgmxMjsHAD0AFQApBoAggDUu9sExACe%2B5hD24ezxFZUYGAbO3sIBATRCiBLS8AYwa4EAHQkYBLABeS1UkgA4gB2ADMTCoAE4mPcVkt7uiAEwwvzINxYbZrGFOAgIYgmIZLLAANzwCwA%2BlTikRiH8EITsJjsQxcWZMASiSSyRTiIZ0KgWKz2ZycXi%2BYTiaTyQQlgdDAp%2BMQJWyYRytmssTLefyFULlUoAI4mRgLSU66Xc2XGuxDYiYVhSvUGh1G%2BXIFhMEkezb6rk8/HywVKpZoaKHW264NesNyommcx0smCPBseP25PGxk2EhB%2B4fAnYYMAIQia02TjW2Hrmy4a0rGgb9crGNbkgbldrNcrTnLTfbCJrjbWCIAIlOABwN2fj%2BcdqdQ2vDmHT8uzucAMQ3q5nrYX2F3VdP/c2g%2BHDarNbrfdrCJ7I9r3bn9bPU5bCK/V9/W9sH7SsYTWOd22/BEXwRBcthdEwbG2AAVVAhGiBAwlpMQCShfstj2bY0AYM4qFoVAA22AB3PB0BJUhtkwvBgCeQl8ODAi9lQ9DMOIbDaGocjKKohiyIoghGMmbYQGo2iSQgKjpkYzBmKeCAECktY8K02c9U43Y6TpalaUwQztjEgMMW2VBojCAMSAgSZBPEmFtliPwCE07T9MI8yhIkkV8HJfltwUc1KioCB3MEP5VG2FY3NQDzYoJDFK0S5LjgS6L/mOfV0pyv5gXijKYuBSY2PuXy9gsiSmGBbMTBJELtgDQwMUiwrDgYwrVAqmF2Oq3Zau2BpMCpANEhapg0HVKKkrK7Ylm2QK8HJfr2KqoayyfNtnyPes517CtW3bOcWxO0DWyAqswM2SD%2B33cDjqvG832PSsXtrA9K1PJt1wgt9Ng/L8l0Ap9mynICQLAiDFynGC4IeaqRpMFqIHqxrmvy7YAFk6WUABJKTlogKyEvxompISmi6O1QahpGqkWrGiamiWvGCcJkqmJYghKp8wjXQIOYGG2f0AGtTNqjEIBMBiqQ2rbdh09EoVnAb0S2HbgMPT6jw5BELvrP9np3cCD0fScPtPc8TzfOcwOPScjtbY2p13SR3sO46lxrP9y3vQ9J0hvbJy7Ht7gQpCACVRXFZQFoICFGDs5lcIZ3YTBI5j/HQbYPO2JRMHQAWOOR7Y44YMUWETjyU/8EVmTlnPgDzgvBCLqSZOL9AIAULz%2B3VrWK8M4z6TpPyXOs2ym4cpziLObP4jbkuO4khgpMXiStMz3zIyGEAQCwKgmBMWgCAzeOWDpRhgFGFajH7zAS6VwW9gPggj%2BzvANRv10xBGTwC6PAwQmqJHlLVdk2x8BDAgAAWi4BoP4GgGJIJQW/CuQsjB/FgaIYgfdN5l2Vr5YWotxZMClnSWqMIICwIIBAYgRglL0MYcwhirCmHAEmJgwiqs9TD01npTYVIkr52iHxQQuNMAsBIIcRQTBgCYAgNvIiCAKgJSGIozAg8SHxGBKZCSVBXSmWCIcAgmAy6EX0YY/YqByi0DpGYixVi9hpiYNgYgxASB0gku4ukWiRYKBau46RLAU4EEJgwfgEB9QADZjEvyceYzADF4lEAcckixmCSG/22CoswTAhCIQWAoYJtxCTbn8YE9au8SGEQkR5SK%2BoMSeO8eEIihSwkRKifwcyTA6AKDSRiCIwS1gRCcAwFpDF3ERLaSQMokjgAFPTDUgevChqYFUIQCAXANkq2HsIwiYoTDBHoOZExRlgjoxOWcnRiTTEpNcbsW55yMmAPQNcyp%2BTXk6PeY45xlihEV1%2BdsckJcrktX%2BZC%2BBFykmfOeW5SRBBmkYgxGEuRYKFDaNajvEZbwsVr2%2BfqCIVBRImJaiSqgeNKwMX%2BZSkZ1Lcb9gmVMtFpB6lDS0Uohi4L0CQuWkgjEkgUEcyFSK1BcLTKfLFRoYVorBVyolXS%2BxHzrmKvlRoWVmqcmHPLvcQu/o/COQziPQiZYITKDkA7OJ4FZwu17JWLggdTrgUgqOE8R4FybGOheV1PlC7jyBXvWZmACDTnGiZWJGI4lBv2Z08wEaaQLGUN46ISKbJhFsKcRFobw2RpTWmrNeBTjRriRIzNlQS1DJgQWnRiKhjoCPmgJqxp5SpQxIoPwwBtiWrkDJFpbaiQZtntmhQfxmBsCHcORtR9HC0GIT5MsTg5DTh2LtD8x0PVHRdXtOGId2y/S9bWX1nq9oBs7pmWwbAABqYRc2FJjtna9mAIl3quIkMtV7syYHffG2dKBZg7yJO2lpK612V2fT%2B7Y774j2A7dOlaUHb1hFlRoLV8pQNor%2BIOzDw7GHId/ahkl2wkEaBJqRjDIHh0AfnYuiuga%2BKMmIO%2Bh9iamNhDfWEODDAy3oA4yxsI/6CBNsA62vDt40XgZ2NOATMHuOJAHWixD/G8DMffWhqj9Zh0tJw8piTPyBMaZI2RijSDEO0ergu4FJCdo%2BVUTY3x2wAgtTI%2Bh9D9HCKFwYO4XxfFTgtRrDZnyGp8mFzwJU9sA0C7yh8zfA41a2IxbSvlCq3ksFuIEGcQutNsZbm2HEyQkreZPBapIOGwKhoJo8YyQQTmtGVAYkMGyiLCIjX%2BchaD3zJUNCYK8EunWp3dc89tZaR5Zz611hHH18M5wXgdk7e1Ps3act2O47AtWqgAIsWWhrnlWtuMKRtxgW23Q7fic16IOT367EaVImRciFFKOjRiUIGo%2BRiHIqIJoLTrsZd2DrK8nYt1TkNu7U2O7ZuWy/CbCbttwLzdmy2Sb/YPow6nFWB6U4nZAUOot1crsnWrf2IqQ%2Bx860MiZMWIkNDoH8pygoCAAQ/tVfW5tmOxQSB9z2/Gvh6WquEU/kfVUJE/4QCF2JrMRg6SEDTtTpwy9c5rw8uyCA5GOU3YF1r3YEuW1S%2BADLixc9iDykV6vfOKudRM%2BmMT7XAv6dJ3HaEe%2BvGbea7t1VquNc66CAbnL4g4uf0QB87QWg0QDg8N53sfh/3qvHcEBztABD%2B5ECuwdtbR3NtCEONyUkAg8AGJTzZKPGfzDx4INgAw/X0CDeUfE3r1fa9NeGM34v6ei4iebUBxDLSEo%2B4kkoxuk0BD7B/UpqyBmG9KBr116jkmrIsAUOMyZLT28da6yl/LU%2BBs/pG9VHWu5ofjc9d%2BOb9skeR11mjo8mOnwIhxxDN6d5WwPn/PtT6QMQbwyNlDCGgFywwxurf6IzE4S5BqU5Fgm407%2BTdg6gwIy7%2BhKJ0hoBc6M7M576%2BRs4naJ5c4p6tAl6mru4fyk5fwgAi7qgkAsB0J0gM5/DO7GosI0GO5/COCOQcIIHaLIGoCoF0GYAu5sEoRoQYRYSiACS5YIAMQlaeQEEx6s6Z7YGc7J6XYEFYGCDZ657eIMAF7KLKHt6qEV5V7T615lrb4z5sDN6tCt5p6VZcqd5ibAbabz4JQ8QiE4TkF/zD5iwvrj6IamG1497KaL7L5sqwF7yC6qq0D%2BH5TfJ%2BG77Bax53YEAYpHBPZ15orwhG6tSh6oDfYQJoos6ja7rdhhy3TXSP7XTmywQn6HTBgzan5%2Bq6yQwBy7RgTVgE7diPia4AaFjMiQL%2BRgRwEIBMEeRoEEHdFU5QFOAyzQJDHZicEoEEKjF6HyEJ6KHc7DAyH87a6642SHDUG0H0Gu7sG0GsFKRDEHF8EMEEGC4kFd6xjUFzFIELHoBO6XFHHwGPGmTPHjqnGSEcFPHcGLG8H8E8Lt6yFDT6E4FKGp4qErHCA57IB55aGF66E2HVT6GV59ZGFB716GE77mEd6WEd5t5om%2BQAYtoOFYYYgJQuCxjdp2KMRxASTeEIaT54lmEpiOEIZBGsqr6knhEOJRGb7bixFsAYHmpjZQQLhHouy1G%2BwI7VFrhTjurPRQxFGuqXStG46ti2qmyNHtjXj5LH4f67S2r7ogRmkg7HiAwE6o5fi36Q7hzeruz3RTgRAAHgSHQqmOzgTg7rgIjem9jNEHq/7Wwvi/QVSa5lihb9zaFOZ4AtRRbpQJntpkZJYRYpZpS6JRnLTWJ2EUkBEYj96slz4FyFkyQvaIbnGO7jKVgRYRDTgpQGbTIllcnVkjG1n1mNl5Slktm4alntmCBL4RB1njKNnFTNn5Gr45ke4qy9nKbwLQKEyIF8guCoEVn9ltn/FfGAkvGdljlNnzkYjDIT4DnblcGoH7kNl/A9lcm/abntqWboDWZhF7A7R6rE46zbog4RwmkuwLhVFQRmkIgOx2nP57peyriVi6lv6unXjE7knd6TkYioQOKjSqDFDgIj4skPnDrr5DZHnizDkr5orilvmSmw6X4gQdHyln6/TmyfiengWOq9ia76ERouioB7E87LFl6bYcUHBcVF7WF7wx4kJkJMLbBJlqy6T6razrBbCbBCC4zbCD5pxTRoAsDRB0CoYSV5wyTthbDYAYWmDsxgJ0D5yaXaX0DEArRhqiwlwGX3CpqoDAAigsB2UixMKOVSUjxdpGC9pWoyTISnAGAoSsV1iro7BPpZhTqwaKakYYh/DdhbDSbbCyZqaobxXwZcBJUpXlxKUqVqVNwaXijWW6X2XeVNq%2BVGUmVYVizmW0CWVlU6W2V6U%2BWGWbAuVuWsCeUOXVWdXJHHDkg4qURvYkAfbZG5HwZ8quYaARAoLubobkovyublYjJ/ARBxIjLoY0oqpoXfJSD37IJbU7VarMrOVJyqWpwlXYVj6kZ/BzhPUxpEX3AuF8SiH7Aiii6UGeGj5sAyRcCPUwgYiOyvVbBDVYqjV1RUCZGfY5GeEySzXfIYjzWLVLUrUpjbhSBQgbWnURC7XMr7U4SHWSDHWbXbUE3nXsS0mHD0lECMlnAsmSCwh/AwhQhexBFbDFkaAVlA0RCSAg3HnbDwJQh/BSBypRAi1xJ/AIgc1ypSSLlwHLk4prmLF83nR/BxLnQMQwhbV/CSARBziRldVXVcAVmi3i1FYjIMTwIy1y3W220wgoJcAQR7Ii1LkrnbBq0vEVlQhziPXobSDbCg0ipziFYm3FkYgW323y3C3wLO0aCu1J221JWC3a3G0e3K1e0%2B0EoQCSBA1xIp0h1zhi0F2SCR1XUwgW2J3J11Ai1p0wgZ0MQIibVcBxK5WK2e2q27l51xKt2u0IjC0RAD0whzgIiV2FySAW2N3N3bCt0RDt25W21i0j0aBQhd3Z093rn5IE0LURB6261FZt0T2XWFwRAVkL1L3x2r0BlQgt0oKl1cAT1Z3YDbAq1KLe290VkjJA0F1xJoKSBJVyp7Jn2dxxIW233r0P0aBP0IgMRi1xJN2wib1v0f2rnf270E2PVzhS1cARBp3%2B2T2dxQiX2P1QjP0INa3IMwgr1a1cBQjjioPv052YMQAwhGzi1PW60Yhi0MPG1gMSRzh%2B3UNxKwh0Md2MNS3wILVD1AMYjMPoNf071kzH25VS0wjt1/Cwgb2CPz2QP0NSO22yPCrsoi1i1GwwiG2KOsMqNiNs0xrC1WMir36n3c1m2835IyOy2mM32y1cBWNS2t2OwYgIgV2v0sPb3q35JhObWC3125XIJFZxLEMSRcDm1eMWMBOG0P0hNhMMQLVzig0UM2NRO%2B271k0oI22kas1uOm2Fy5VkN5PB2FPFP13wJJUBMj2lOf250Vns0ioE3B0xoB1ooV16MBMVmtNP2p3i163wMi1A3j1Gwb0RNKN9P5Kh3JVmOg0LVN0KMTPT1eOdPzO21LPQQMMFNs0lNrO2PRMQAc0oIwgaALOPMUOMOpOkYX1ePnMrNXPs313O1QhQgRA3NK1oN3PlMQCL0iod0LP4MnUoMTMQO73XOAvaMgsUOzMEMaCaM9MYMqNj1p3L0h0Igy3oYHPuMNOkP5JAuYvtNJU4uaMMTAPr1xJTC3NlN50xrIIaAs1oJQgy1j0m0ohojCKrAOy9h1FXjBi4MMVH7Wxw6zZ%2BpbCwrIDRBozAAatviVjhnylhMVG7QviuzWy1EviKuvhPiMXmk6lAFTaWnGlfRzhXgiryvByUUyl2z0UE4AVzhAy6n2oOmQR%2BxUUWxmzFnFV/Uvq7panbDvX8RfVqgeHszRvH42nWxg5pv1hbCogcDTC0CcARC8CeAcBaCkCoCcBpX1iZnpQKCXALCpSaO8Bfylt5vTASwgCSBh1j1G3pOwNJ3pMAMFscCSDFuaC8AVscC8BvCoIttaDTBwCwAwCICAZaWtXkCUBWWtUgAMBUjIDIC5UYgmB1A0AXzcaUDBDjukDBB%2BAVCHBXs3vMBHAADywQ2gTInAPApAmlbAggz7DAtA97rbpAWAYCwATgn2bw3AvAWA/oRg4gwH%2BAroJQjIUHZbWymFFiV7HkDQV7tAoCIoRwLgWAV7CWLAn7ebfABgwACgN6JaVEz7tkJbX7/Aahoh7AUgMgggigKg6gwHugdQBgRgKAhS%2BgoCbwsAk67A%2B7uIpAzGZBmYEsPC5b4eiQUH8CFQiJlSzAtgjIE7zGfEWAEnbBRQJQ9gEAjg/QtQpAPgowuQ%2BQaQcQCQAgVnjnGQiQ7Q9nXQgwY0ZnzQwwrnPnjQpQwwnnnQ4QgwAXrgNQege2YX4wEX0wdbcwCwEg%2BbhbY7wHk7zmN6Tgw4uVyV4t%2BSuAhAJAjbeyzb47ynHbBN%2BgnAo7pA5HMIztzXrXbX7XADJbZbk707IAs7VXpAi7K7QGGrBAG7Fn%2BAzItEegrHwg7HEg0gs3PHagV7AnpAVEIo0QA3w7RbpAXXE7nAz7TUo31k1KAQuX%2BXeVRXEALga7Nl5XkwlXrbynmETAWA4QbBw7DX5Hgtj1oLAtzzDDqN2t99%2B35bnAvX/Xz3g3y76k5OyaqSFAKiLVNlAQrAiw53eXiVhXX7QaIAygyEAAGjZ5NyQNNye7ICIGIBx4t7IMt3x2W2t6Z00A4NXIFzZ9XPFw53UOkM50kNFwMDEE500Fz95/UMFwIC0H0AL9Z8zyF60KLxFz0K0Oz3F3Z%2BF2l6QAcC/NN%2Brjt5l915wEmiZDBvR6hpj5dzj8V6T7ZSGPXbd%2BVbb1iBV1r1V%2BlyO7wOR9hhBEbFiIVmEyPf7VEGDz15YH16789%2B25EAtUvQXU9UbLBE9Tk8OzCAbwd1OxH/O%2B7xiGn%2BDxn3O223JwpvYJIEAA%3D"></iframe>

## 개요 
- 최근 (2023년 3분기) [thrust](https://github.com/NVIDIA/thrust) 가 [CCCL (CUDA C++ Core Libraries)](https://github.com/NVIDIA/cccl) 이라는 상위 라이브러리에 편입되었다. 
- thrust 는 CUDA programming 을 좀 더 modern C++ 스럽게 할 수 있게 지원해주는 library이다. 
    - 예를 들어, [공식 깃허브에 있는 예제](https://github.com/NVIDIA/cccl#example)를 가져와 보자면, 커스텀 커널을 작성하는 것보다 훨씬 간결하고 안전하다. 
      <p align="center">
        <img src="/assets/data/2023-12-10-thrust2/why-thrust.png" width="1300"/>
      </p>
    - ps. GPT4에게 thrust 와 같은 고수준 API를 사용하면 좋은점에 대해 좀 더 설명을 부탁해보았다. 

        Thrust와 같은 고수준 라이브러리를 사용하는 것은 커스텀 CUDA 커널을 직접 작성하는 것보다 몇 가지 중요한 장점이 있습니다. 여기 두 가지를 포함하여 몇 가지 장점을 설명해 드리겠습니다:

        1. **자동 동기화 관리**: Thrust와 같은 고수준 라이브러리는 내부적으로 필요한 동기화를 관리합니다. 이는 프로그래머가 CUDA 스트림과 동기화 메커니즘에 대해 신경 쓰지 않아도 되도록 해줍니다. 예를 들어, 데이터가 GPU에서 CPU로 또는 그 반대로 전송되는 경우, Thrust는 적절한 동기화 점을 자동으로 삽입하여 데이터 일관성을 보장합니다.

        2. **간결하고 읽기 쉬운 코드**: Thrust는 C++ 표준 템플릿 라이브러리(STL)과 유사한 인터페이스를 제공하여, 프로그래머가 익숙한 형식으로 GPU 코드를 작성할 수 있게 해줍니다. 이렇게 함으로써 코드의 가독성과 유지 관리가 향상되며, 오류 발생 가능성이 줄어듭니다.

        3. **효율적인 메모리 관리**: Thrust는 자동 메모리 관리를 제공합니다. 이는 프로그래머가 메모리 할당과 해제를 직접 관리하는 복잡성을 피할 수 있게 해줍니다. 또한, Thrust는 메모리 접근 패턴을 최적화하여 GPU의 메모리 대역폭을 효율적으로 활용할 수 있게 합니다.

        4. **성능 최적화**: Thrust는 내부적으로 다양한 최적화 기법을 적용합니다. 예를 들어, 병렬 알고리즘의 구현에서는 블록 크기, 공유 메모리 사용 등 여러 요소가 자동으로 최적화됩니다. 이는 프로그래머가 이러한 세부 사항에 대해 신경 쓰지 않고도 효율적인 GPU 코드를 작성할 수 있게 해줍니다.

        5. **넓은 범위의 병렬 알고리즘 지원**: Thrust는 정렬, 감소, 스캔 등 다양한 병렬 알고리즘을 제공합니다. 이를 통해 프로그래머는 복잡한 알고리즘을 쉽게 구현할 수 있으며, 이는 GPU 프로그래밍의 진입 장벽을 낮추는 데 도움이 됩니다.

        6. **휴대성과 호환성**: Thrust 코드는 다양한 CUDA 버전과 GPU 아키텍처에서 호환됩니다. 이는 프로그램이 다양한 하드웨어 및 소프트웨어 환경에서도 안정적으로 작동하도록 보장합니다.

        종합적으로, Thrust를 사용하면 프로그래머가 복잡한 하드웨어 세부 사항에 대해 걱정하지 않고, 고성능 GPU 애플리케이션을 더 빠르고 쉽게 개발할 수 있습니다.

- [앞의 블로그 튜토리얼]({{ site.baseurl }}{% post_url 2023-04-08-thrust-practice-1 %}) 에서 직접 빌드하고 실습하는 예시를 하나 다루었었다. 
    - 그런데 C++은 참 재밌는데 늘 빌드환경 구축하는 게 귀찮다. 그래서 공부하는 데까지 마음먹게 되는 것이 초보자들에게 장벽인 듯. 
    - 게다가 CUDA공부를 하고 싶으면 NVIDIA GPU를 가지고 있는 머신에서 해야 하니, 만약 이런 머신을 가지고 있지 않다면 역시 장벽이 되겠다. 
- 그래서 Compiler Explorer 를 추천한다. 


## Compiler Explorer 란 
- Compiler Explorer 는 Matt Godbolt 라는 분께서 만드신 툴로써 온갖 언어의 컴파일 및 실행을 수행해주는 웹 툴이라고 보면 된다. 
    - 현재 다양한 언어를 지원하고 있지만 보통 C/C++ 유저들이 많이 사용하고 있다. 
    - 상세한 역사에 대해서는 저자직강의 다음 영상을 추천한다.
        - [What's New in Compiler Explorer? - Matt Godbolt - C++ on Sea 2023](https://youtu.be/O5sEug_iaf4?si=1kIP2QyA9ZoWsNcQ)
    - 사용예시 
        - [C++ Weekly - Ep 332 - C++ Lambda vs std::function vs Function Pointer](https://youtu.be/aC-aAiS5Wuc?si=p-BBBluttITKHEr0)
            - Lambda vs std::function vs Function Pointer 의 실체는 다를까? 에 대해서 설명하고 싶을 때,
                - 컴파일을 해보고 오른쪽 창에 나타나는 항목에서, 어셈블리 언어 수준에서 다른 이유들을 분석해준다! 아래는 좀 전 유튜브 캡처:
                  <p align="center">
                    <img src="/assets/data/2023-12-10-thrust2/cppweekly.png" width="1000"/>
                  </p>
            - 그런 설명들을 할 때 유용한 툴이 되겠다.  
- ps. URL도 [https://godbolt.org/](https://godbolt.org/) 이며 그래서 그냥 Compiler Explorer라고 안부르고 godbolt 라고 불리기도 한다.
            
### 요약
- 암튼 여기에서 하려고 했던 이야기는 Compiler Explorer는 CUDA build (NVCC) and run 도 지원한다. 


## 실습
- [NVCC 컴파일러를 세팅해둔 이 링크](https://godbolt.org/z/x4G73af9a) 로 들어가자.
- 그리고 [여기에 있는 코드](https://github.com/gisbi-kim/thrust_examples/blob/main/three-dim-point-projection-to-image.cu) 를 복사해서 붙여넣은 다음에, 결과를 관찰하자.   
    - 이 예시는 point cloud 를 spherical image 에 projection 하는 예제 코드이다. 
        - 일반적으로 수십만~수백만 그 이상의 포인트에 대해 CPU에서 이 작업을 수행하려면 매우 느릴 것이다. 
    - 구현체를 좀 더 들여다보자. 


### 구현 
- [전체 코드](https://github.com/gisbi-kim/thrust_examples/blob/main/three-dim-point-projection-to-image.cu) 중, 실제 작업하는 부분은 다음과 같다. 
  <p align="center">
    <img src="/assets/data/2023-12-10-thrust2/impl1.png" width="1000"/>
  </p>
    - 어려울 것이 1도 없는 코드이다. Functor 작성한 다음에 ::transform 의 인자로 넣어주면 끝! 
        - 그러면 `thrust::transform`는 array를 순회하며 동일한 작업을 수행하는 아주 전형적인 functional programming style 의 구현이다.
            - 그런데 이제 GPU에서의 병렬처리, 블록 나누기, 동기화같은 걸  알아서 다 해주는!
            - 담는 자료형이 std::vector가 아니라 thrust::device_vector 라는 걸 사용한다는 것 정도만 신경써주면 된다.  
    - ps. 위의 구현은 GPT4 가 다 해준 것이다! See the [작업기](https://chat.openai.com/share/dd336223-c23a-4d50-992b-d0edf340918d)
        - 덕분에 cuda 및 thrust 의 API 이름들을 다 모르는 상태에서도 바로 공부를 시작할 수 있었다. 
        - Tip: 다만, 할루시네이션이나 빌드가능하지 않은 (pseudo-like code) 코드를 줄 때가 있는데, 그런 부분이 지난 ChatGPT 출시 1년 동안에 코드 작성시 도움받을 때 약간 우려되는 부분이긴 했다 (출시 이후 계속 많이 좋아지긴 함). 그럴 때 검증용으로 바로 godbolt 에 붙여넣고 빌드가 되는지 확인하면서 왔다갔다 하는 방식으로 공부하니 효율이 좋았다. 


### 실험환경 
- 현재 (2023.12.10) 무려 16G의 T4 위에서 (무료로) 실행해주고 있는 것을 알 수 있다... ㅎㅎㅎ
- 확인하려면 (이런것도 GPT4에게 물어보면 다 알려준다)
    ```c++
   // GPU 속성 가져오기
    int device;
    cudaGetDevice(&device);
    cudaDeviceProp properties;
    cudaGetDeviceProperties(&properties, device);
    std::cout << "Using GPU: " << properties.name << std::endl;

    // CUDA 버전 가져오기
    int runtimeVer;
    cudaRuntimeGetVersion(&runtimeVer);
    std::cout << "CUDA Runtime Version: " << runtimeVer / 1000 << "." << (runtimeVer % 100) / 10 << std::endl;

    int driverVer;
    cudaDriverGetVersion(&driverVer);
    std::cout << "CUDA Driver Version: " << driverVer / 1000 << "." << (driverVer % 100) / 10 << std::endl;
    ```
- 결과:
            
        Using GPU: Tesla T4
        CUDA Runtime Version: 12.2
        CUDA Driver Version: 12.2
        
### 실험 세팅 
- Point 를 N개 랜덤 생성하고, 대충 정한 이미지 (640x480) 에 projection 하는 예시.

### 실험 결과 화면
- godbolt 에서 수행 시 다음과 같이 결과도 출력된다. 
  <p align="center">
    <img src="/assets/data/2023-12-10-thrust2/result.png" width="1500"/>
  </p>

### 실험 결과 (수행 시간) 
- 결과 값 자체는 예시이니 큰 의미없고 시간을 측정해보자. 
- `cudaEventRecord` 라는 걸 사용하면 device (GPU)에서 이루어지는 code block에 대해서도 시간을 측정할 수 있다고 한다.  
    - 예시 
      <p align="center">
        <img src="/assets/data/2023-12-10-thrust2/time_impl.png" width="750"/>
      </p>
- **Varing `N`**, 각 3번씩 수행:
    - N = 1000
        ```
        Memory usage at before allocation: used = 105.000000, free = 14825.562500 MB, total = 14930.562500 MB
        Memory usage at after allocation: used = 109.000000, free = 14821.562500 MB, total = 14930.562500 MB

        * Point generation time: 0.036864 ms
        * Spherical transformation time: 0.060384 ms
        * Copying to host time: 0.032896 ms
        Total execution time: 0.130144 ms

        * Point generation time: 0.016064 ms
        * Spherical transformation time: 0.017056 ms
        * Copying to host time: 0.02736 ms
        Total execution time: 0.06048 ms

        * Point generation time: 0.016192 ms
        * Spherical transformation time: 0.016768 ms
        * Copying to host time: 0.024384 ms
        Total execution time: 0.057344 ms
        ```
    - N = 10000
        ```
        Memory usage at before allocation: used = 105.000000, free = 14825.562500 MB, total = 14930.562500 MB
        Memory usage at after allocation: used = 109.000000, free = 14821.562500 MB, total = 14930.562500 MB

        * Point generation time: 0.038912 ms
        * Spherical transformation time: 0.057376 ms
        * Copying to host time: 0.091008 ms
        Total execution time: 0.187296 ms

        * Point generation time: 0.018336 ms
        * Spherical transformation time: 0.017312 ms
        * Copying to host time: 0.086208 ms
        Total execution time: 0.121856 ms

        * Point generation time: 0.018464 ms
        * Spherical transformation time: 0.01664 ms
        * Copying to host time: 0.086176 ms
        Total execution time: 0.12128 ms
        ```
    - N = 100000
        ```
        Memory usage at before allocation: used = 105.000000, free = 14825.562500 MB, total = 14930.562500 MB
        Memory usage at after allocation: used = 109.000000, free = 14821.562500 MB, total = 14930.562500 MB

        * Point generation time: 0.064512 ms
        * Spherical transformation time: 0.09008 ms
        * Copying to host time: 0.683456 ms
        Total execution time: 0.838048 ms

        * Point generation time: 0.042784 ms
        * Spherical transformation time: 0.045728 ms
        * Copying to host time: 0.723328 ms
        Total execution time: 0.81184 ms

        * Point generation time: 0.043008 ms
        * Spherical transformation time: 0.045056 ms
        * Copying to host time: 0.670624 ms
        Total execution time: 0.758688 ms
        ```
    - N = 1000000
        ```
        Memory usage at before allocation: used = 105.000000, free = 14825.562500 MB, total = 14930.562500 MB
        Memory usage at after allocation: used = 127.000000, free = 14803.562500 MB, total = 14930.562500 MB

        * Point generation time: 0.354464 ms
        * Spherical transformation time: 0.399936 ms
        * Copying to host time: 4.54739 ms
        Total execution time: 5.30179 ms

        * Point generation time: 0.323712 ms
        * Spherical transformation time: 0.342688 ms
        * Copying to host time: 4.5623 ms
        Total execution time: 5.2287 ms

        * Point generation time: 0.323584 ms
        * Spherical transformation time: 0.341408 ms
        * Copying to host time: 4.63437 ms
        Total execution time: 5.29936 ms
        ```
    - N = 5000000
        ```
        Memory usage at before allocation: used = 105.000000, free = 14825.562500 MB, total = 14930.562500 MB
        Memory usage at after allocation: used = 205.000000, free = 14725.562500 MB, total = 14930.562500 MB

        * Point generation time: 1.76397 ms
        * Spherical transformation time: 1.72032 ms
        * Copying to host time: 20.9988 ms
        Total execution time: 24.483 ms

        * Point generation time: 1.7425 ms
        * Spherical transformation time: 1.67654 ms
        * Copying to host time: 20.9689 ms
        Total execution time: 24.388 ms

        * Point generation time: 1.6937 ms
        * Spherical transformation time: 1.62406 ms
        * Copying to host time: 21.2419 ms
        Total execution time: 24.5596 ms
        ```
    - N = 7000000
        ```
        Memory usage at before allocation: used = 105.000000, free = 14825.562500 MB, total = 14930.562500 MB
        Memory usage at after allocation: used = 243.000000, free = 14687.562500 MB, total = 14930.562500 MB

        * Point generation time: 2.51101 ms
        * Spherical transformation time: 2.38698 ms
        * Copying to host time: 29.7932 ms
        Total execution time: 34.6912 ms

        * Point generation time: 2.48816 ms
        * Spherical transformation time: 2.34291 ms
        * Copying to host time: 29.6979 ms
        Total execution time: 34.5289 ms

        * Point generation time: 2.49011 ms
        * Spherical transformation time: 2.34176 ms
        * Copying to host time: 29.7214 ms
        Total execution time: 34.5533 ms
        ```
    - N = 9000000
        ```
        Memory usage at before allocation: used = 105.000000, free = 14825.562500 MB, total = 14930.562500 MB
        Memory usage at after allocation: used = 281.000000, free = 14649.562500 MB, total = 14930.562500 MB

        * Point generation time: 3.10112 ms
        * Spherical transformation time: 2.92096 ms
        * Copying to host time: 37.5393 ms
        Total execution time: 43.5613 ms

        * Point generation time: 3.07869 ms
        * Spherical transformation time: 2.87117 ms
        * Copying to host time: 37.7315 ms
        Total execution time: 43.6814 ms

        * Point generation time: 3.08115 ms
        * Spherical transformation time: 2.86979 ms
        * Copying to host time: 37.6602 ms
        Total execution time: 43.6111 ms
        ```
    - N = 10000000
        ```
        Program terminated with signal: SIGKILL
        ```
        - 천만개는 godbolt에서 실행을 안해준다... ㅎ
- 위 결과를 그리면 (3회들의 평균값):

  <p align="center">
    <img src="/assets/data/2023-12-10-thrust2/time_plot.png" width="750"/>
  </p>
    - 이 실험을 통해 우리는 이미 알고있던 사실을 다시 ([_godbolt 덕분에_](https://godbolt.org/z/jMYs16nGq)) 빠르게 알아볼 수 있었다.
        1. GPU는 병렬 작업을 빠르게 처리한다. 
            - task마다 다르겠지만, 900만 포인트를 3d에서 2d (spherical image)로 projection하는 연산이 T4에서 3ms 정도 소요되었다. 
        2. [thrust 의 API](https://github.com/gisbi-kim/thrust_examples/blob/main/three-dim-point-projection-to-image.cu)를 이용하면 이러한 이득을 프로그래머 입장에서 아주 손쉽게 취할 수 있다.
        3. CPU와 GPU사이 copy는 매우 느리다. 
            - ps. 요즘 그래서 [HBM](https://www.google.com/search?q=hbm+cpu+gpu+copy&sourceid=chrome&ie=UTF-8)이라는 단어도 핫한 듯 하다. 
            - 위의 예시에서는 input point cloud 를 random 하게 생성하는 것도 device-side 에서 `thrust::transform`을 통해서 수행하여서 역시 시간 소요가 매우 적었다. 

## 결론 
- godbolt 는 아주 좋은 사이트이다.
    - share 도 가능하다. 교육용으로 아주 좋음. 
      <p align="center">
        <img src="/assets/data/2023-12-10-thrust2/share.png" width="750"/>
      </p>
    - godbolt cppcon 라고 유튜브에서 검색하면 Matt Godbolt 선생님의 아주 좋은 강의들이 또 많이 있다. 
- [thrust 실습](https://godbolt.org/z/jMYs16nGq)을 이어나가보자. 
    


