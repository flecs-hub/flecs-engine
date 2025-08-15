# Flecs Engine
A game engine based on the [Flecs Entity Component System](https://github.com/SanderMertens/flecs).

**Want to contribute to a new game engine and get paid for it? Come discuss the project on the [Flecs Discord](https://discord.com/invite/BEzP5Rgrrp)!**

### A canonical, opinionated, Flecs-first engine
Flecs Engine will not be a general purpose engine like Unreal, Unity, Godot or Bevy. The primary goal of the engine is to offer the fastest possible path from plain Flecs code to an application with graphics, sound, UI and user input. 

Engine functionality will be exclusively accessible through ECS building blocks. The Flecs API will not be wrapped, and the engine will not expose functions outside of the Flecs API. For an example of what this looks like in practice, see the [tower defense demo](https://github.com/SanderMertens/tower_defense/blob/master/src/main.cpp).

If the engine hits a limitation in Flecs, it won't work around that limitation. Instead this will either be an accepted limitation of the engine, or will result in changes to Flecs. This will ensure that code remains straightforward, and act as a discovery mechanism for Flecs shortcomings.

The engine won't come shipped with a wide range of graphics options, but will instead aim to do a small number of things well. As a result the engine will likely end up having its own distinct visual style, which is OK.

The use cases for Flecs Engine will be to:
- serve as example code for other Flecs projects
- run Flecs demo applications
- support development of simple games
- provide an option for (future) Flecs game jams

### UGC
A lot of game engines primarily target professional software developers and artists. This generally increases the quality of the content created for the engine at the cost of a smaller target audience. Flecs Engine will do the reverse: focus on UGC (User Generated Content) with less sophisticated but more accessible tools.

The minimum skill level for a Flecs Engine content creator will be roughly the same as someone able to write HTML/CSS. Flecs Engine will feel like a "browser for gamedev", where instead of HTML/CSS you write Flecs Script. Implementing game mechanics will still require coding in any of the supported languages.

Rather than supporting a small number of complex entities, Flecs Engine will support a large number of simple entities. For example, it should be possible to create UIs that consist out of tens of thousands of rectangles, lines and text boxes. Similarly it should be possible to create 3D objects that are composed out of many primitive shapes, where each primitive shape is its own entity.

### Run everywhere, for everyone
One of the goals of the engine is to run Flecs demos, which in most cases will be viewed on (mobile) browsers. Therefore the engine will have to restrain itself to technologies that are stable and widely adopted (so sadly no webgpu). Running a Flecs Engine application should be as simple as opening a URL.

Code should run unmodified on all supported platforms. Developers should not have to spend time on porting their code, toggle code between platform-specific compiler flags etc. This may result in the application developer having to give up some control to the engine (such as the application's main function), which is OK.

### Features
Not final, but likely something close to:

- Primitive 2D & 3D shapes
- Meshes
- Textures
- PBR
- Directional, spot, point and ambient lights
- Cascaded shadow maps
- Post process FX (fog, ssao, bloom, hdr/gamma)
- Instancing
- Object picking
- Audio

## Technical constraints

### Programming language
The engine will be implemented in C where possible, and in C++ where necessary. In places where C++ is used, C++ features will be used conservatively. STL should not be used unless this cannot be avoided. A good reason to use C++ is if an engine module depends on a library that only provides a C++ API. A bad reason to use C++ is that code will be cleaner/faster to write.

Because significant portions of the engine will be written in C, all engine headers need to be C compatible.

The main reasons for using C as primary language are:
- the C ABI can be easily ported to other languages
- C compilation speeds are much faster than C++, which leads to faster iteration times
- C has no templates, which forces all engine features to be available at runtime
- C is a stupid simple language, which means less time spent on bikeshedding API design
- Because C is simple, there is a larger pool of potential contributors

While C is the primary language for the engine, C++ applications should be fully supported and convenient to write. See the [tower defense demo](https://github.com/SanderMertens/tower_defense/blob/master/src/main.cpp) for an example that's built with mostly C modules that have thin C++ wrappers.

To ensure the engine can be used from C, C++, Rust and C#, the components exposed by the engine cannot use non-trivial types (e.g. no `std::vector`). While engine modules internally are allowed to use more complex language features, the external interface must be compatible with the C ABI.

### Graphics API
The graphics API should be webgpu based. Options are:
- sokol
- wgpu / wgpu-native

