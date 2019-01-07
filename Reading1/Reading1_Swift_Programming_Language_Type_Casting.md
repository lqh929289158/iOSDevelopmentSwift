# Type Casting

- A way to check the type of an instance.
- Treat an instance as a different superclass or subclass.
- Check whether a type conforms to a protocol.

`is` `as`

## Defining a Class Hierarchy for Type Casting

```swift
class MediaItem {
    var name: String
    init(name: String) {
        self.name = name
    }
}

class Movie: MediaItem {
    var director: String
    init(name: String, director: String) {
        self.director = director
        super.init(name: name)
    }
}

class Song: MediaItem {
    var artist: String
    init(name: String, artist: String) {
        self.artist = artist
        super.init(name: name)
    }
}

let library = [
    Movie(name: "Casablanca", director: "Michael Curtiz"),
    Song(name: "Blue Suede Shoes", artist: "Elvis Presley"),
    Movie(name: "Citizen Kane", director: "Orson Welles"),
    Song(name: "The One And Only", artist: "Chesney Hawkes"),
    Song(name: "Never Gonna Give You Up", artist: "Rick Astley")
]
// the type of "library" is inferred to be [MediaItem]
```

> WARNING: If you iterate over the contents of `library`, the items returned are typed as `MediaItem`.

## Checking Type

_Type check operator_ `is`. Return `true` if the instance is of that subclass type.

```swift
var movieCount = 0
var songCount = 0

for item in library {
    if item is Movie {
        movieCount += 1
    } else if item is Song {
        songCount += 1
    }
}

print("Media library contains \(movieCount) movies and \(songCount) songs")
// Prints "Media library contains 2 movies and 3 songs"
```

## Downcasting

Motivation: A constant or variable may actually refer to an instance of a subclass.

_Type cast operator_ `as`. `as?` `as!`

`as?` returns an optional value of the type you are trying to downcast to. Return `nil` when downcasting fails.

`as!` attempts the downcast and force-unwraps the result as a single compund action. Runtime error when downcasting fails.

```swift
for item in library {
    if let movie = item as? Movie {
        print("Movie: \(movie.name), dir. \(movie.director)")
    } else if let song = item as? Song {
        print("Song: \(song.name), by \(song.artist)")
    }
}
```

> NOTE: Casting **DOES NOT** modify the instance. It is simply treated and accessed as an instance of the type casted.

## Type Casting for Any and AnyObject

- `Any` represent an instance of any type at all, including function types.
- `AnyObject` represent an instance of any class type.







