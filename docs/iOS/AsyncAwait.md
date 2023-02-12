# Async Await

## Async Await이란
- 기존의 비동기 처리 방식인 DispatchQueue나 Closure 및 completionHandler를 사용했지만 콜백 지옥이나 이해가 힘들고 예외처리에 어려움이 있어서 실수하기 쉽다. 이를 해결하기 위해 더욱 편하게 비동기 처리할 수 있는 문법이다.

## 기존 문법의 복잡함

1. 많은 비동기 작업
    - 일련의 비동기 작업에는 deeply-nested closures가 필요하다.
    
    ```Swift
    func processImageData1(completionBlock: (_ result: Image) -> Void) {
        loadWebResource("dataprofile.txt") { dataResource in
            loadWebResource("imagedata.dat") { imageResource in
                decodeImage(dataResource, imageResource) { imageTmp in
                    dewarpAndCleanupImage(imageTmp) { imageResult in
                        completionBlock(imageResult)
                    }
                }
            }
        }
    }

    processImageData1 { image in
        display(image)
    }
    ```

2. 오류처리
    - 콜백은 오류 처리를 어렵고 매우 장황하게 만든다.
    
    ```Swift
    func processImageData2c(completionBlock: (Result<Image, Error>) -> Void) {
        loadWebResource("dataprofile.txt") { dataResourceResult in
            switch dataResourceResult {
            case .success(let dataResource):
                loadWebResource("imagedata.dat") { imageResourceResult in
                    switch imageResourceResult {
                    case .success(let imageResource):
                        decodeImage(dataResource, imageResource) { imageTmpResult in
                            switch imageTmpResult {
                            case .success(let imageTmp):
                                dewarpAndCleanupImage(imageTmp) { imageResult in
                                    completionBlock(imageResult)
                                }
                            case .failure(let error):
                                completionBlock(.failure(error))
                            }
                        }
                    case .failure(let error):
                        completionBlock(.failure(error))
                    }
                }
            case .failure(let error):
                completionBlock(.failure(error))
            }
        }
    }

    processImageData2c { result in
        switch result {
        case .success(let image):
            display(image)
        case .failure(let error):
            display("No image today", error)
        }
    }
    ```

## 해결
- 위와같은 문제를 해결하기 위해 async-await proposal은 Swift에 코루틴 모델을 도입. -> 비동기 함수의 semantics 정의 하지만 동시성을 제공하지는 않음
- 비동기 코드를 마치 동기 코드인것처럼 작성 할 수 있다. -> 프로그래머가 동기 코드에서 사용할 수 있는 동일한 언어 구조를 최대한 활용 가능.
- 자연스럽게 코드의 의미 구조를 보존 -> 언어에 대한 최소한 3가지 교차 개선에 필요한 정보를 제공

## Async, Await을 처리하는 내부 원리 (스레드 관리)
- async에서의 스레드 관리
    1. 호출: A함수에서 B함수(async)를 호출하면, A함수가 실행되던 스레드의 제어권을 B에게 전달 
    2. 진행 - B함수는 async이기 때문에 스레드의 제어권을 포기하는 suspend가 가능 (suspend되면 호출한 A함수도 같이 suspend됨)
    3. suspend - 스레드에 대한 제어권은 system으로 가고 시스템은 스레드를 사용하여 다른 작업을 수행
    4. resume - 일시 중단된 비동기 함수 B를 다시 실행하는 단계
    5. 종료 - B함수가 종료되면 A함수에게 스레드 제어권을 반납

![스크린샷 2023-02-12 오후 9 45 30](https://user-images.githubusercontent.com/45002556/218311936-a09e12e6-cba3-4697-9d18-6bc02d1f839c.png)

## 사용법
- 메소드 시그니처에 async를 붙여서 비동기 작업임을 알리는 키워드를 추가
- 메소드 내부 구현에서 비동기가 예상되는 곳에 await 키워드를 추가
- 해당 메소드를 호출하는 쪽에서는 async block에서 호출하고, await키워드를 붙여서 호출

```Swift
// 기존 코드
func processImageData1(completionBlock: (_ result: Image) -> Void) {
    loadWebResource("dataprofile.txt") { dataResource in
        loadWebResource("imagedata.dat") { imageResource in
            decodeImage(dataResource, imageResource) { imageTmp in
                dewarpAndCleanupImage(imageTmp) { imageResult in
                    completionBlock(imageResult)
                }
            }
        }
    }
}

processImageData1 { image in
    display(image)
}

// async/await
func loadWebResource(_ path: String) async throws -> Resource
func decodeImage(_ r1: Resource, _ r2: Resource) async throws -> Image
func dewarpAndCleanupImage(_ i : Image) async throws -> Image

func processImageData() async throws -> Image {
    let dataResource  = try await loadWebResource("dataprofile.txt")
    let imageResource = try await loadWebResource("imagedata.dat")
    let imageTmp      = try await decodeImage(dataResource, imageResource)
    let imageResult   = try await dewarpAndCleanupImage(imageTmp)
    return imageResult
}
```
