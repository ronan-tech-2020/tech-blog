# How to test React component with Context

## Why have this article

In my team, we're not using context that often. So there is not many examples that I can refer to to test some context components. When I worked on my ticket, I stuck with testing. This will be my notes for how to write such testing.

## Code example

Where to mock
How to mock it
How to use the mocked context object

## Issues

- [ ] enzyme supports shallow component with context value provided as options, but it doesn't seems working properly
- [ ] mock in `it` test level won't work, I guess it's import order issue. I guess, the context is imported in before the mock module part, because when test runs, it shows default values. But figure out how it is actually compiled, it's mystery to me.
      so we have to mock it before `context`
- [ ] can I mock it before `it`. but in `context`

## References
