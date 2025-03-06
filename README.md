# Flecs Engine
A game engine based on the [Flecs Entity Component System](https://github.com/SanderMertens/flecs).

**Want to contribute to a new game engine and get paid for it? Come discuss the project on the [Flecs Discord](https://discord.com/invite/BEzP5Rgrrp)!**

## Project Goals

### Equitable and scalable open source development
Many open source projects float on the contributions of an army of volunteers. While this has some advantages, there are many downsides to this model:

- Reaching consensus is challenging, which can lead to indefinite postponement of complex decisions
- The number of contributions often outstripts review capacity, which leads to stale PRs and sad contributors
- Contributions often don't align with project goals or schedule
- Managing a large community takes a lot of effort, which is time that's not spent on code
- Projects offer no substantial rewards for contributions, and instead rely on the passion of contributors
- All of the above too often causes maintainers and contributors to burn out

There has to be a better way. You don't need hundreds of people to build something cool. A small, focused team of talented engineers can build incredible things in a short amount of time. The development process for Flecs Engine aims to replicate what makes small teams successful, but for open source.

The process in a nutshell:

- The project roadmap will be managed by a small (1-3) number of maintainers.
- GitHub issues with well defined scope and requirements will be created by maintainers
- Issues will get funded through [bountybot](https://bountybot.dev/)
- Bounties will be anywhere between $25 and $1000, depending on how much effort or complex an issue is
- The first contributor to successfully implement the issue receives the bounty
- [Flecs sponsorship](https://github.com/sponsors/SanderMertens) money will be the primary source of funding (at least initially)

This approach addresses the aforementioned issues:

- The project direction is at all times clear and unambiguous
- Contributors have a guarantee that if they solve an issue their work will get merged
- Pull requests that don't meet requirements don't have to be reviewed, reducing the load on maintainers
- Contributors get paid for doing things they love
- There are no additional expectations of contributors, reducing the risk of burnout

Just like Flecs itself, the engine will be [MIT](LICENSE) licensed.

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

### A browser but for gamedev
ECS lends itself uniquely for data-driven workflows that are not dissimilar from how web browsers work. In a web browser you have a DOM, which is a set of elements that have attributes and can have child elements. In ECS, there is a set of entities that can have components and child entities. [Flecs script](https://www.flecs.dev/flecs/md_docs_2FlecsScript.html) is to ECS what HTML is to web browsers.

Flecs Engine will showcase this idea, where all you need to do in order to render something is write a few lines of Flecs script. This is not fundamentally different from creating entities and components through the regular C/C++/Rust/C# APIs, but does enforce that engine features cannot rely on functions that can only be called from C/C++/Rust/C#.

Rather than supporting a small number of complex entities, Flecs Engine aims to support a large number of simple entities. For example, it should be possible to create UIs that consist out of tens of thousands of rectangles, lines and text boxes. Similarly it should be possible to create 3D objects that are composed out of many primitive shapes, where each primitive shape is its own entity.

While the engine will have support for meshes, the primary focus is on large numbers of primitive shapes. This ensures that anyone who can write HTML/CSS can also create a scene for Flecs Engine, while also showcasing the large number of objects an ECS can handle.

### Run everywhere, for everyone
One of the goals of the engine is to run Flecs demos, which in most cases will be viewed on (mobile) browsers. Therefore the engine will have to restrain itself to technologies that are stable and widely adopted (so sadly no webgpu).

Applications written for the engine should run unmodified in the browser as well as standalone application.

## Technical constraints

### Build system
Cmake will be used as build system. While I have strong opinions personally about cmake, it's hard to find a build system that better supports the gamedev ecosystem. Support for other build systems can be added on an ad-hoc basis.

### Programming Language
The engine will be implemented in C where possible, and in C++ where necessary. In places where C++ is used, C++ features will be used conservatively. Usage of STL should not be used unless this cannot be avoided. A good reason to use C++ is if an engine module depends on a library that only provides a C++ API. A bad reason to use C++ is that code will be cleaner/faster to write.

Because significant portions of the engine will be written in C, all engine headers need to be C compatible.

The main reasons for using C as primary language are:
- the C ABI can be easily ported to other languages
- C compilation speeds are much faster than C++, which leads to faster iteration times
- C has no templates, which forces all engine features to be available at runtime
- C is a stupid simple language, which means less time spent on bikeshedding API design
- Because C is simple, there is a larger pool of potential contributors

Note that while C is the primary language for the engine, C++ applications should be fully supported and convenient to write. See the [tower defense demo](https://github.com/SanderMertens/tower_defense/blob/master/src/main.cpp) for an example that's built with mostly C modules that have thin C++ wrappers.

To ensure the engine can be used from C, C++, Rust and C#, the components exposed by the engine cannot use non-trivial types (e.g. no `std::vector`). While engine modules internally are allowed to use more complex language features, the external interface must be compatible with a C ABI.

### Graphics API
SDL3 GPU will be used for graphics. This ensures that the engine will be usable on a wide variety of platforms, with a large pool of potential contributors.
