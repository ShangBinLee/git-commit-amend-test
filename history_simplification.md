History Simplification에 관한 정리 
================================

## 발단

```
git commit --amend
```

에 대한 2번 테스트를 진행하다가, Desktop에서 강제 Push한 것을 Notebook에서 Pull하여 기존의 수정된 커밋을 그대로 보유한 상태로 원격 저장소와 로컬 저장소를 Merge하게 되었다. 

그 상태에서 Notebook에서 다시 Push를 하니, 정상적으로 Push가 완료되었고, GitHub 상에서는 test.html 파일에 대한 Commit History가 의도했던대로 출력되지 않았다.

```
git commit --amend
```

를 통해 수정한 커밋이 아닌 수정 대상이었던 기존의 커밋이 최신 커밋으로서 File History에 나오게 된 것이다. 

따라서 원인을 찾아보게 되었는데, 실제로 

```
git log --graph
```

를 통해 출력한 History 상에서는

M   <--- Merge Commit
|\
| A   <--- Amend Commit
|/
O   <--- Original Commit(modified by A commit)


이런 식으로 기준 브랜치와 Merge 된 브랜치 상의 커밋이 모두 출력되었다. 

하지만 test.html을 대상으로 한 

```
git log --graph test.html
```

에서는 

O
|
|
|
C   <--- Other Commit(previous of Original)

이런 식으로 출력되었다.

중요한 것은, Merge Commit, Amend Commit이 생략되었다는 것이다.

이는 History Simplification 때문이다.


## History Simplification에 관한 References

__https://git-scm.com/docs/git-log#_history_simplification__

__https://localcoder.org/git-history-for-branch-after-merge__

__https://errorsfixing.com/git-commit-history-of-a-file-showing-the-right-commits/__

__https://docs.microsoft.com/en-us/azure/devops/repos/git/git-log-history-simplification?view=azure-devops__

__http://ohyecloudy.com/pnotes/archives/git-file-log-history-simplification/__


###### File History Simplification Default Mode
History Simplification에는 Default Mode가 존재한다. git-scm의 설명은 이렇다. 

__Default mode__
Simplifies the history to the simplest history explaining the final state of the tree. Simplest because it prunes some side branches if the end result is the same (i.e. merging branches with the same content)

해당 트리의 최종 상태를 설명하는 가장 간단한 히스토리로 만든다는 것이다. 만약 최종 결과가 똑같다면 몇몇 사이드 Branch들을 쳐내기도 한다. 

여기서 최종 결과가 같다는 말이 무슨 말인지 이해가 안 됐는데, 기본적으로 merge를 하게 되면 충돌을 해결하는 등의 과정을 거친 후 브랜치들을 병합하게 된다. 따라서 최종 결과는 merge를 한 이상 같아질 수밖에 없는 게 팩트다. 

그렇다면, merged된 branch가 있을 경우, 이 Branch의 Commit들은 모두 history 출력 시 쳐낸다는 것인데, 개인적으로 테스트를 해본 결과 꼭 그렇지만은 않았다. 

__microsoft azure__ 의 docs에서 설명하는 바에 따르면 merge시에 충돌이 발생하고 이에 따라 side branch의 커밋으로 인한 파일 수정 내용을 적용하지 않고 merge를 했을 때, File History는 Side Branch의 Commit과 Merge Commit을 모두 생략하게 된다.

이것으로 볼 때, 내 생각으로는 최종 결과가 똑같다면 몇몇 사이드 Branch들을 쳐낸다는 얘기는 결국, 그 사이드 Branch를 쳐냈을 때 현재 파일의 최종 결과에 영향이 없다면, 즉, 최종 결과와 동일하다면 사이드 Branch를 쳐낸다는 뜻이라고 본다. 

이것을 증명하기 위해서 이번에는 반대로 충돌이 있었을 때 기준 브랜치와 사이드 브랜치의 커밋으로 인한 파일 수정 내용을 모두 그대로 적용하고 Merge를 했는데, File History에서는 Merge Commit과 Side Branch Commit이 모두 출력되었다. 

만약 Merge Commit과 Branch Commit을 쳐내게 되면 당연히 파일의 현재 최종 상태와 차이가 발생하게 되므로 이것을 File History 상에 포함한 것으로 보인다. 

즉, 파일의 최종 상태에 영향을 주는 커밋은 포함하고 빼도 상관없는 커밋은 뺀다는 말이다. 

그래서 테스트를 한 번 더 진행해봤는데, 이번에는 충돌 시 기준 브랜치의 커밋으로 인한 수정 사항은 제외하고 사이드 브랜치의 커밋으로 인한 수정 사항은 적용해서 Merge 해봤다. 

이번에는 기존의 기준 브랜치의 커밋은 제외되고, 사이드 브랜치의 커밋이 포함되어 출력되었으며, Merge Commit은 제외되었다. 

또한 흥미로운 점은, 사이드 브랜치의 커밋이 마치 기준 브랜치의 원래 커밋이었던 것처럼 표시되었다는 점이다. 즉, 사이드 브랜치로 갈라지는 게 아닌 하나의 줄기로 사이드 브랜치의 커밋이 기준 브랜치의 커밋들과 이어져 있었다. 

S
|
|
|
A
|
|
B

이런 형식으로 말이다. 

즉, File History Simplification은 철저하게 파일의 현재 최종 상태에 영향을 주는 Commit만 고르는 것이라는 사실을 알 수 있었다. 

이것은 파일의 최종 상태에 영향을 주는 커밋만 걸러서 볼 수 있도록 해서 어떤 커밋이 유의미한 수정 사항을 만들어냈는지 아는 데에는 도움이 되지만, 사이드 브랜치와의 Merge와 파일에 적용된 전체 History를 일부 생략하는 단점이 존재한다. 

따라서 

```
git log --full-history
```

명령을 통해 전체 History를 볼 수 있다. File History에도 물론 

```
git log --full-history [file-name]
```

의 형식으로 적용할 수 있기 때문에 전체 History를 보기 원한다면 위의 명령어를 사용해야 한다. 


또한, GitHub 상에서는 History Simplification Default Mode가 적용되는 것으로 보인다. 

이 말은, Commit Message 수정을 위한 Amend Commit과 Merge시 충돌로 인해 생략된 수정 사항을 가리키는 커밋 같은 경우 GitHub File History에서는 제외될 수 있다는 것이다. 

그러므로 GitHub를 통해 File History를 보는 것은 완전히 정확하진 않고, 전체 Commit History를 보거나 실제로 Git Bash에서 Full History를 봐야 정확하게 볼 수 있다. 

결론은 Amend Commit을 통해 기존의 Commit Message를 수정할 것이라면, 그 수정 사항을 원격 저장소에 강제 Push할 때 다른 Contributor들의 로컬 저장소에서도 

```
git reset --hard [commit id last before amended commit]
git pull
```

명령어 등을 통해 완벽하게 Amended Commit을 log 상에서 삭제하고 pull 하여 기존의 커밋이 다시 등장하게 되는 불상사를 막아야 한다.


혹은 revert -> recommit(revert -> new branch -> cherry-pick -> merge or cherry-pick) (revert -> show reverted commit | apply -> commit)
하는 방식을 통해 원격 저장소에 충돌을 일으키지 않으면서도 commit message를 수정할 수 있다.

다만 이 방식은 기존의 commit이 제거되는 게 아니라 history 상에 남아있으면서도 revert commit으로 되돌린 사실을 알리는 방식이기 때문에 조금 지저분하게 보일 수는 있다. 

따라서 이 방식을 이용할 때는 revert commit과 recommit의 commit message를 commit의 이유를 정확하게 적어 혼란을 방지해야 할 것 같다.