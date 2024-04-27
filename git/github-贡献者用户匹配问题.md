# github贡献者用户匹配问题

1. 当没有配置`user.name`和`user.email`时，在mac上取了本机操作系统的`Full Name`作为了贡献者的名字（除了insight中的贡献者之外，其他需要标记是谁提交的代码时用这个名字），但是在github的insight的贡献者中看不到，没有该用户的信息(提示：We need at least one non-empty commit with an email to generate this graph.)。

2. 当仅仅配置`user.name`，没有配置`user.email`时，用了`user.name`配置的名字作为了贡献者的名字（除了insight中的贡献者之外，其他需要标记是谁提交的代码时用这个名字），但是在github的insight的贡献者中看不到，没有该用户的信息(提示：We need at least one non-empty commit with an email to generate this graph.)。

3. 当仅仅配置`user.email`(但是该email没有和提交账号的email匹配，不一致)，没有配置`user.name`时，在mac上取了本机操作系统的`Full Name`作为了贡献者的名字（除了insight中的贡献者之外，其他需要标记是谁提交的代码时用这个名字），但是在github的insight的贡献者中看不到，没有该用户的信息(提示：We need at least one non-empty commit with an email to generate this graph.)。并且如果此种情况和第一种情况都发生了，在github的insight的pulse中，可以看出虽然名字一样，但是它认为是2个人。

4. 当仅仅配置`user.email`(但是该email和提交账号的email匹配)，随便配置了一个`user.name`时，贡献者直接识别了该Email对应的github对应的账号，并且关联了所有贡献者到这个github账号下面。所有地方关于贡献者的功能都正常，可点击跳转到github对应用户的个人页面。

---

==自己尝试总结==
