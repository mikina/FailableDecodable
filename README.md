# Swift failable Decodable playground

Swift playground file and source code for the YT video "Swift failable Decodable".

```swift
import UIKit

let json = """
[
    {
        "id": 1,
        "name": "Any vs. AnyObject in Swift",
        "type": "articles"
    },
    {
        "id": 2,
        "name": "New Apple silicon",
        "type": "news"
    },
    {
        "id": 3,
        "name": "Passing weak self into a closure",
        "type": "videos"
    }
]
""".data(using: .utf8)!

struct ListElement: Decodable {
    let id: Int
    let name: String
    let type: ElementType

    enum ElementType: String, Decodable {
        case articles, news
    }
}

struct FailableDecodable<T: Decodable>: Decodable {
    let element: T?

    init(from decoder: Decoder) throws {
        self.element = try? T(from: decoder)
    }
}

struct FailableResult<T: Decodable>: Decodable {
    let result: Result<T, Error>

    init(from decoder: Decoder) throws {
        result = Result(catching: { try T(from: decoder) })
    }
}


let elements = try JSONDecoder().decode([FailableResult<ListElement>].self, from: json).compactMap { try? $0.result.get() }
print(elements)

```
