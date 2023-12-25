---
layout: post
title:  "🌈 CUDA 프로그래밍: Thrust 실습 2편 (Feat. godbolt)"
date:   2023-12-10 23:00:00 +0000
categories: Productivity
---

# Compiler Explorer (godbolt) 에서 편하게 연습하자
- 난 CUDA 및 [Thrust 공부를 빠르고 편하게 + 어디에서나](https://godbolt.org/z/jMYs16nGq) 하고싶지 
- 빌드환경을 구축하는 건 귀찮다...
- 라는 마음이 들 때

## 티저
- ... 라는 마음이 들 때
- 아래처럼 실습을 하면 된다. (컴파일이 되는 동안 잠시 기다려야 한다)

   <iframe width="1100px" height="700px" src="https://godbolt.org/e?readOnly=true#z:OYLghAFBqd5QCxAYwPYBMCmBRdBLAF1QCcAaPECAMzwBtMA7AQwFtMQByARg9KtQYEAysib0QXACx8BBAKoBnTAAUAHpwAMvAFYTStJg1DIAruiakl9ZATwDKjdAGFUtEywZ7HAGTwNMAHLuAEaYxBIAHBqkAA6oCoR2DC5uHnpxCbYCvv5BLKHhXFGWmNZZDEIETMQEKe6eXCVlSZXVBDmBIWGR0QpVNXVpjX1tHXkFPQCUlqgmxMjsHAD0AFQApBoAggDUu9sExACe%2B5h924ezxFZUYGAbO3sIBAQxCiBLS8AYwa4EAHQkYBLABeS1UkgA4gB2ADMTCoAE4mPcVkt7uiAEwwvzINxYbZrGFOAgIYgmPpLLAANzwCwA%2BlTMDYSH8EITsJjsQxcWZMASiSSyRTiIZ0KgWKz2ZycXi%2BYTiaTyQQlgdDAp%2BMQJWyYRytmssTLefyFULlUoAI4mRgLSU66Xc2XGux9YiYVhSvUGh1G%2BXIFhMEkezb6rk8/HywVKpZoGKHW264NesNyommcx0smCPBseP25PGxnM4hB%2B4fAnYYMAIQArGtNk41tgG5suGtKxpGw3Kxi25JG5W67XK05y82Owja021giACLTiKNucT%2Bed6dQusjmEz8tziIAMQ3q9nbYX2F3VdPA82Q5Hjartfr/brCN7o7rPYiDbP09bCK/V9/W9sAHSsYTWKJF2nF8EQXLYXRMGxtgAFVQIQYgQMJaTEAkoQHLY9m2NAGDOKhaFQANtgAdzwdASVIbYMLwYAnkJPDg3wvYULQjDiCw2hqDIijKPo0jyIIBjJm2EAqJokkIEo6YGMwJinggBBJLWXDNLnPUON2Ok6WpWlMAM7ZRIDDFtlQGIwgDEgIEmASxJhbY4j8AgNK0vSCLMwTxJFfByX5bcFHNGoqAgNzBD%2BVRthWVzUHcmKCQxSsEqS454qi/5jn1NLsr%2BYE4vS6LgUmVj7h8vZzPEphgWzEwSWC7YA0MDEIoKw56IK1RyphNiqt2GrtlKTAqQDJJmqYNB1UixLSu2JZtgCvByT6tjKsGssn3bZ8jwbCI%2BwrNsOwiVtjtAtsgKrMDNg7YDwIPQ6n2vK632PSsjqvA9K1PZt1yiN9Ng/L8l0Al7APLECwIg78EWg2CHiq4aTGaiA6oapq8u2ABZOllAASUkpaIEs%2BK8cJyT4uo2jtQGwbhqpZrRvG8pFtx/GCeKxjmIICrvII10CDmBhtn9ABrEyaoxCATHoql1s23ZtPRKE5369Etm2h7H0%2Bo8OQRc6Gz/cDJB3R7DynD7T3PE83wiMDjynZ7KyN6ddzN/bTcg5c/3Le9LebVtdqnbte3ueDEIAJVFcVlHmggIUYWyiGIHD6d2ExiKY/x0G2dztiUTB0H59ike2GOGDFFh4/cpP/BFVPZez4Bc/zwRC8k6Si/QCAFE8gc1c18uDKM%2Bk6V85yrJsxv7McoizizhJW%2BL9vxIYSSF/EzSM58yM%2BhAEAsCoJgTFoAgM1jlg6UYYA/D5YgjD7zBi8VgW9n3ghD6zvANWv10xCGTwC6PAwRGpJHlDVdk2x8B9AgAAWi4BoP40RthIJQW/cugsjB/FgaIYgvcN6lyVj5IWIsxZMElnSGqMIICwIIBAR%2BwBFL0MYUYFhwCGFMMmJggiKs9RDw1rpTYVJEp5xiLxQQONMAsBIIcRQTBgCYAgFvQiCBqjxRGEogeJCEjAhMuJKgroTLBEOAQTApcCJ6IMfsVAVRaB0lMeYyxew0xMGwMQYgJA6TiTcXSEYwsFDNTcdIlgScCAEwYPwCA%2BoABsRiX6OLMZgeicSiD2KSeYzBJDf7bBUWYJgQgEILAUEE24hJtx%2BICWtHeJCCISPchFfUGIPFePCIRApoTwmRP4GZJgdAFCpIxNWIJaxqxOAYM0%2BibjwmtJIJUSRwB8npmqf3Xhg1MCqEIBALg6zlZD2EQRMUJhgj0DMsYwywQ0bHNOZgSYCSTHJJcbsG5Zz0mAPQFcipeTXl3PeQ4pxFihHl1%2BdsckxdLnNX%2BZC%2BB5zEmfOea5SRBAmkYgxKEuRYKFCKL5BRfUIzpLgrzt8/FVARLGOaqS3GlZ6L/MpcMqg1KxkTKmXUwaWiUlYohZ89mSCMSSBQbyjQ/KUHkvhVcpafKBUaCFSK1B0KeWSuFdK2V0rskHLLvcAu/o/AOXTsPAiZYITKDkPbWJ4E5zOz7K7f2J1wL3THCeI8C5NhHQvHa7yBcx5At3jMzABAZxjWMjEjEsTvV7I6eYQNNIFjKC8TEJF1kwi2FOIiv1Aag2xvjcmvApwQ2xIkUmmoubBkwMzXcxFfR0CHzQI1Y08oUoYkUH4YA2xjVyGks0%2BtRJE0zxTQoP4zA2DdpHFWw%2BjhaDEO8mWJwcgZw7B2h%2BI6jrDq2t2rDR1v1nV1jdU63anqO6ZlsGwAAamENNBSo5Z2PZgcJZ6rhJHzUe7MmB70RrHSgWY28iQNuabO%2BdFdr0vu2PehI9hG0juWkB09YQhUaBlfKX9aK/hdsQz2xh0HX2wfxWg%2BDxNcOQY/ROqd5cvW8UZMQe9F6o3kbCHesIYGGD5vQLRyjYR30EGrZ%2ButaHbxov/TsGcrGQMMaSJ2tFkGWN4Ao/euDCGf09uaShiTvGfmsdkzhpBGh8NIMI5x8dVdJ3ApIdtbyqjrE%2BO2AEZqWn4PwZIwRAuDB3A%2BN4qcZqtZjPeQ1HkgueAKkdn6vneUznr4HBLaxYLqU8rlS8lg1xAgzgFxpljLc2xYmSFQTzJ4zVJAQWBYNSN7jGSCEsyMGo9E%2BjWURQRYa/ykLAe%2Bag0oTBXjFwa8OprDmtpLSPHOPWD0w6usghEC89tHaWoOta1s79dhuOwCV2oADzH5vKx5GrriCkLcYEtt0K24lVZiNk2bSL3IYqOAopRIaMShA1Li2gZFRDlGacd%2BLuxtZXi7Mu6cBs3Ym1XSNg8j4rb9ZtuBMbI3g5fXdldI8VZ7oDgRI7ICB0JurhdjNt7%2BxFQHyPuWhkTJU6QL8mBHUMC6TZQUBAAIr3CvzcW1HJkJBe5rYjXwuLhWCKf0PqqYif8IDc%2B41mIwdJCApxIPKJeOdV7uXZBAbTpA2Wc%2BVx/HHX8he2BF2L2exYiRS5XnnWXOpqfTCVyr5X6AKcJwHaEO%2BTHTcnfN4Vyu1da6CHruL4gAuX0QGcw9mIBweFs72PwrH9OduM7QAQvuRAjsbbm1txbQhDjclJAIPA%2BiY/WWDwn8w23BDYAMG19AHXlFxJa8X0vlX%2BgEGr9n%2BPhd9Pce/Q2RTaL4pu/EkohuE0BD7BfeJyyqmK9KBL41hTfHLIsAUMyyZaKG/1ca9FtLI/2svu61VbWu4gdfmNqDkbEPvxnXDg9D6u/pzw6fEj6c/5BxvTvG2B8t/Ngvk%2BkDEGPtwY60hjtGG90lwIxm6C7eoE5FjE5iQ9hk6W7Zg4p0hoDM5U404b4%2BTh6CCR7M4x5tA576qO6q6mg84ih84kAsB0JW7uQ26YB24OT0SW6U5/CODUHk4wFKJwGoAIF/C266qKRcToSYSiD8QpYID0TZYeTYGh506J4R5M7R6HbYGoHCAp7IBp4MAZ7KKyEN7yGF6taj6l75qr5j5sDV5tB15x4FbspN61ot5IYYjxTcR8HYS87qjEG96iw3qD6Qb6Gl6QZdrT6z7NIL52JiBeF5TfKeHr5eZY4NJSIyJyKXZl5orwjmJpxiCPYuEvbIGGq9YXQ9ghw3Sw46w3jmwwROrOzBjDZH7urf435QxthgQ1jo49iPgnYfqFhE5Eg0LQIIBkGCCIHYEtGE4S7tF%2BSQHYAMSi7%2BgsHwEEK9EaGSFoHSEs415iEc4q6C4xiHCkF0GcH240HdEEADoMGKRdFbGUFcHYFc5q41rWQbHQETEmRTHoAUFUEsLjGwEPEHFVyMFdHMH3FsHTEcGnH27LE6Rh5zEEDoEyGx5yFgnJ6p5eIqGZ7qFmFVSaFF46E%2B7l5olr6GGN7GGN717Ik%2BQfqWHeHt7bAuCxgtq2IMTxDiRuEQbD5YkGEpit6T5iwz7jJz6QG7xc6BG0DBHL7bhhFsAZF7Bb4rhbqlE7oH4lHGzrgIgOrex%2BwP5hy5G1H36hzmomw6wdjXh5J9avg7TmqwwgTGnfbHiAzo6I5fiX4A6hwupux3TTjVg1Gfj2ro4wx/bymKnPTKlThOnanGyv4RDlQnZlg%2BZ9yqGWZ4DNSBZpQxkNpaaRb%2BbRapQ6JhlLRWIWFfqkkYid4MkT75y5nSTXaQbHHW5jKVj%2BbVgzjJSqZTIFmsljGU6VnVm1m5SFkNmoaFnlnkGtljK1lFT1lorlTz4ZlO7KydkSbwLQIEx3Hkl/GPElndlNnfF3GsEIH9k1l1lTkYhDJD49mvGTGLkclVkDl/AdlNnpEqaFlEaGYN7bQapm7awrrfaqmezOwLjFFwzGkIj2zWkqmnQfldhanP66m1jNHZk8a7koT2IjSqBMjgJ970krkNqL6da7nsl%2BHz4RGFYvkw4DYgSNFHS2y/TmxulukXTWp9gnbyGBouioAbGs6zF56Lb0UHCMVZ6mG7yh4kJkKPzbBxmqwgnDyrD3CbBCA4zbDd4pyTRoAsAxB0Cwb8W5zSQdhbDYAIWmBsxgJ0B5zyWKX0BpwqXFxqX3BxqoDAAigsDLT%2BoiymWCXDzNpGBtomrSRISnAGDIQ0X1hzo7BXpZjDqgZiZoIYh/A9hbACbbBCbSawbBXgZcBhURVlznbHDkg4otTiS3YkD3apHlCEqj42YaDVgoJ2bwZiopjbhSARDDJ/DVixLDLwbUq0p8k2aSBI7IL1WNUyo4xsSpVYoZUUSJGwYpGoBPYhVErNQYhnSlVlUVVtUZYIh1UNXVhNW9UtVwXfJSAdXLXdVMrCId4JzSXJyNyTT0kwh/BIJcCJVYVbC2G8G8T8H7CEFOGaguH95sDSRhUIgYgaAIixK3WbDxQUmHBUlEA0lnDnVQh1UwhI6A2wXYSbKIVsz0mSAXX1VcAuS%2BEHXbD5kyWnXIUD7bAXUaBQgRCxJ/nY1A3bB2GPUOEvV/zvX0lhURBQjXVQiA3A3XFg2oAQ10lE0wjQ2whcAulU0I20DwXI1nVE1o1/CxJnRmxU0oi41HX41M0C0oJnQi2c000PV8TPVqiM0o1E0s0U1QiU0z53ULmUkuXg0IC0kfXsDE3Q2xKxLCrw2tVI3aXS2fXbCy2xLXVcCA3mVHUaAllcB1Vo1or0TwLQ1SDCrVgx2xJ/AIhQiZYYiSQzlk5zkZUuAIHh0zXy2NDE31V/CSDVghkh0FxcAlmx2XXp2J3bDwLJ2p3p0x0k1FAaC7JN2znzl53TEllk1/BRAaDSDbDTUCrk2SChmbD5kYi10t1p3Crt0oKd3F3wJhVo3y0hk93Z190nklmSAR1u3F3TXQ1H3T1V0dwwi10d1RDr2b0wjb30RLXVhcAB0Z272jE51KILn515IU2XURA/X0TVhLWY3AMz35mSC12P3P3bCv3v2JUx3Q1gOk2Z29250H15KrUlXVgwiN0wiZZ1VcAIhQNHXVglmIMf0oN1UKlQgv2a1s1kNf3bA/18j91Ll5LDIR1H2xL0RSBhXCq7JX3iSxK12oP0OMMaCs2kP0Qu1P2wgYN71YP/0QDVirXD0RCN0i2b1k3kMFxQhUNMNyPbAKOxKwi0MB3m3VjKPf371qOw0R1FARD0QwgYhx36OiPbARCD1y2KMwhWNcA2Mx0lU/WSAjmsPsN/0D3cPEOJWEPv1/CwhQgGMdwIgSNy3BMTihMp38rR1N3Q2GxEO2NRMOOxMQAWN/DuMNVuOSACpI5kPeNIK11hP5N7mFMp2Y3l2MMOwYgIjT1lOqMVMDOR0wjF2JXIKZaxJpPiTXWZPFM9MIPD3uMDOgPD1n3d1Z32PDNcPqPtUoLDICOwgp2zOhXGN9NrPbAlU1WyMx1hWY1gN2NsPlN7OC0CqrVj2hoRDhX8pnOY0lk3ObP3OXUEMIgx0R3AOGypNDO/2cNvDcOHS/MdPTUlVP0Z3NMwN5Ib2gtgMQvD3wzBPrOC1bOYNwvYMQBp0oIwh/XyMCps3m3/OUPYuQuEsMPXPVNs1uPJNQjVhs3PPRPwuAtSBZPgtoKaN8swj/PiM4OcvF0XVQi8tctN1hXVi/XjMCuvMIsQAwg1Vl3IPj3/WlUYtbD5nBMlkKtKsP11Xqun2lVQixKlPbMvO7PauhrIKj3svBPJ26sz0ohojCKrD2x9jlFXjBjaPkU74GmSmkWIywrIAxCozACJtviViv4kXTjWpAQgQvjPRWxlEvgg6GkPQUUI5tjGlluVhmnRvQ4RBXgCqRuBwEVg6jZ2zo5fkRBAxamWq2n/7Okn4Dj7imwq0Fxq3G2dYXR1Eji0362OFG0%2B1yhFuWlWy/YGlul3VLAcDTC0CcDVi8CeAcBaCkCoCcBRUNiplpQKCXALApTjO8BfyHtbvTDiwgD1MrPaNa0yNd3XX8M7scCSD7uaC8AnscC8BvDRAPtaDTBwCwAwCICfoKVKVkAUAqLiiGXdAMBUjIDICJUYgmCNA0DnwMaUDBBAekDBB%2BDVCHBkcUfMBHAADywQ2ghOnAPApA8lbAgg9HDAtA1Hj7pAWAYCwATgKRbw3AvAWA/oRg4g/H%2BAroNgsVYnR7XtjUiwR77kpQZHtAoCIoRwLgWAZH4WLArHW7fABgwACgJ6ualE9HNkB7bH/AggIgYg7AUgMgggigKg6g/HugjQBgRgKABS%2BgoCbwsAQ67A2HuIpAFGIABwWc4sPCx7AeSQYn8C1QShFSzAtgjIwHFGvEWAoX1BVghOYmEAjggwDQpAPg984w3QjQGQiQAgFX6Q8QjXDAYwXQhQTQJXAgrQAwrg9QegxXCnvXNeHX%2BQdXlgNezXwwY3NXnXEg0wV7cwCwi3%2Bgu7gH/HIHVmJ6TgI4iV4Vl1eSuAhAJAt7uy97QHiXL7q163/7vAxnMIF1T3L3r3b3/DB7R7IHYHIAEHV3pAMH8HX6ibteKHxcp3%2BXegjnwg/Brn0g0PnnagZHvnpAlEIoMQ/3f7e7pAn3wHnA9HjUIPVkjKAQu3%2B3SVR3EALgiHRl53kwl3j7iXGETAWA4Q1Bf7AHpAxnst2jvLaNXdUIv18tDDuPx7nAP3f3jPAPcHakeOMaKSKHBlSHAQrAiwpPe3oVh3bH3qIAygSEAAGlV/gKnDRFD7IM5%2BIG5wj0oEjz50N6NCN54GV1XDN1V1XONxMPV61%2BUK7w1%2BUB75N8N%2BUH17UAN0MN147yHwH112tq72ttH2twcC/Kbwrlj5t195wNGsZCBtZ7Bur%2BT1r8d8b2dyGMXdT%2Bh2nKX/T6QJB0%2B3d5z8Z8hlEIbFiIteXandozj2R995YL9zX1d8%2ByABo5dQHUfREMA6Q%2BP4dInX%2BzCOn3j6B/34z9u5wBiPP2L4v7X4lxRoxq%2B0AA%3D%3D%3D"></iframe>

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
- **`N 을 바꿔가면서`**, 각 3번씩 수행:
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
    


