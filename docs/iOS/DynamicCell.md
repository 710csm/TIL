# Dynamic Cell Size

## dynamic cell size 적용 예시
- 텍스트 길이마다 크기를 적용하지 않으면 텍스트가 잘리거나 cell이 너무 길게 보일 수 있어서 dynamic cell size를 적용하여 아래 이미지처럼 보이게 할 수 있다.

![img](https://user-images.githubusercontent.com/45002556/133882751-92dd1d14-2179-4cc0-86d6-2c2d9da39117.gif)


## text 길이에 따른 dynamic cell size 

```swift
class ViewController: UIViewController {
    func estimatedFrame(text: String, font: UIFont) -> CGRect {
        let size = CGSize(width: 200, height: 55) // 임시로 정해둔 cell size
        let options = NSStringDrawingOptions.usesFontLeading.union(.usesLineFragmentOrigin)
        return NSString(string: text).boundingRect(
            with: size,
            options: options,
            attributes: [NSAttributedString.Key.font: font],
            context: nil
        )
    }
}

extension ViewController: UICollectionViewDataSource, UICollectionViewDelegateFlowLayout {
    func collectionView(
        _ collectionView: UICollectionView,
        layout collectionViewLayout: UICollectionViewLayout,
        sizeForItemAt indexPath: IndexPath
    ) -> CGSize {
        let width = self.estimatedFrame(
            text: "example",
            font: UIFont.boldSystemFont(ofSize: 15)
        ).width
        return CGSize(width: width, height: 55)
    }
}
```
## systemLayoutSizeFitting을 사용한 dynamic cell size

```swift
extension ViewController: UICollectionViewDelegate, UICollectionViewDataSource, UICollectionViewDelegateFlowLayout {
    func collectionView(_ collectionView: UICollectionView, numberOfItemsInSection section: Int) -> Int {
        return items.count
    }
    
    func collectionView(
        _ collectionView: UICollectionView, 
        layout collectionViewLayout: UICollectionViewLayout, 
        sizeForItemAt indexPath: IndexPath
    ) -> CGSize {
        return CollectionViewCell.fittingSize(availableHeight: 45, name: items[indexPath.item])
    }
}

class CollectionViewCell: UICollectionViewCell {

    private let titleLabel: UILabel = UILabel()

    func configure(name: String?) {
        titleLabel.text = name
    }
    
    static func fittingSize(availableHeight: CGFloat, name: String?) -> CGSize {
        let cell = CollectionViewCell()
        cell.configure(name: name)
        
        let targetSize = CGSize(width: UIView.layoutFittingCompressedSize.width, height: availableHeight)
        return cell.contentView.systemLayoutSizeFitting(targetSize, withHorizontalFittingPriority: .fittingSizeLevel, verticalFittingPriority: .required)
    }
}    
```
