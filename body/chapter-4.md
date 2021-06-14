# Chapter 4

## Principles To Live By

**After completing this chapter, you will be able to**

<!-- Introduction -->

### Dependency Inversion

#### Composition versus Aggregation

If you took classes in Computer Science, you may have stumbled across the topics such as composition and aggregation. While these sound like complicated subjects, they are actually quite simple to explain.

#### Key Differences

| Composition | Aggregation |
| ----------- | ----------- |
| Parents owns child | Child is independent |
| Child has the same lifecycle as parent | Child has separate lifecycle |
| Strong association | Weak association |
| Child is "newed" in the parent constructor | Child is passed to parent |
