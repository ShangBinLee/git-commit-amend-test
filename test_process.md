테스트에 대한 과정과 결과를 기록
===========================

# 테스트는 과정 기록을 위해서 master 브랜치가 아닌 test 브랜치에서 수행함.

## 1번 테스트

#### 테스트 시행 전 상황 

* ##### 브랜치 목록 

1. master(local in desktop)
2. test-amend(local in desktop)
3. origin/master(remote)
4. origin/test-amend(remote)

* ##### 브랜치별 커밋 목록 

1. master

    __1f9fd3dbc212573c30874cbc788bf1df7f05403b__(HEAD -> master, origin/master)   
    __bd8e25414bd55531bb0dc6864c142df944ab1e00__ README.md add 주의   
    __77ede9464455dfe7646161e925d87d0f3618eb46__ README.md add accent ordered list for situations and its expectations   
    __92a323f422b31f3b50d59780eeeb4c55fe0f838f__ README.md add markdowns   
    __8b3c44b1fb91607c9bdc41b0512700a526fb9b1c__ README.md first commit   
    생략...

2. test-amend

    __423b89f29cc2b9f4720d8c067ad4f558f0795239__ (HEAD -> test-amend, origin/test-amend) 앗 실수   
    __bd8e25414bd55531bb0dc6864c142df944ab1e00__ (origin/master, master) README.md add 주의   
    __77ede9464455dfe7646161e925d87d0f3618eb46__ README.md add accent ordered list for situations and its expectations   
    __92a323f422b31f3b50d59780eeeb4c55fe0f838f__ README.md add markdowns   
    __8b3c44b1fb91607c9bdc41b0512700a526fb9b1c__ README.md first commit   
    생략...

3. origin/master

    master와 동일

4. origin/test-amend

    test-amend와 동일

<hr>

#### 테스트 과정 

1. git checkout test-amend

2. git commit -amend 

3. git push origin test-amend 

<hr>

#### 테스트 결과

- test-amend 커밋 목록 

    __0d0dcc20fd4b636650dcdc9d1adfc8e969bacb36__ (HEAD -> test-amend, origin/test-amend) test.html add p tag and its contents   
    __bd8e25414bd55531bb0dc6864c142df944ab1e00__ README.md add 주의   
    __77ede9464455dfe7646161e925d87d0f3618eb46__ README.md add accent ordered list for situations and its expectations   
    __92a323f422b31f3b50d59780eeeb4c55fe0f838f__ README.md add markdowns   
    __8b3c44b1fb91607c9bdc41b0512700a526fb9b1c__ README.md first commit   

기존의 __423b89f2__ 커밋이 __0d0dcc20__ 커밋으로 덮어쓰여짐. 


git push origin test-amend를 진행했을 때 에러가 발생. 

__에러 내용__

    ! [rejected]        test-amend -> test-amend (non-fast-forward)   
    error: failed to push some refs to 'github.com:ShangBinLee/git-commit-amend-test.git'
    hint: Updates were rejected because the tip of your current branch is behind   
    hint: its remote counterpart. Integrate the remote changes (e.g.
    hint: 'git pull ...') before pushing again.   
    hint: See the 'Note about fast-forwards' in 'git push --help' for details.

__hint__ 에서 말하는대로 git pull을 하게 되면, 

    There is no tracking information for the current branch.   
    Please specify which branch you want to merge with.   
    See git-pull(1) for details.

    git pull <remote> <branch>

    If you wish to set tracking information for this branch you can do so with:

    git branch --set-upstream-to=origin/<branch> test

에러가 발생하는데, __UP STREAM__ 설정이 안 되어 있어서 그러는 것이다. 따라서 branch에 설정을 해준 후 다시 git pull을 하면 

    Merge made by the 'ort' strategy.

라는 메시지와 함께 pull이 완료된다. ort라는 것은 3-way strategy로 병합을 하는 것을 말하므로 두 브랜치를 병합한 형태로 pull이 완료된다. 

따라서 __덮어썼던 커밋이 다시 History 상에 등장한다.__

결국, <pre><code>git push -f</code></pre>를 통해서 강제 푸시 하는 것이 유일하게 원격 저장소를 로컬 저장소로 덮어쓸 수 있는 방법이다. 

<hr>

#### 강제 푸시

처음에는 

```git
git push --force-with-lease
```

를 통해 push를 해보았다. 실패하지 않고 push되어 원격 저장소에 로컬 저장소 내용을 덮어썼다. 


기존의 __423b89f29cc2b9f4720d8c067ad4f558f0795239__ 커밋이 아닌 __0d0dcc20fd4b636650dcdc9d1adfc8e969bacb36__ 으로 변경된 모습이었다.


또다시 

```git
git push -f 
``` 

를 통해 기존의 __423b89f2__ 커밋을 포함하여 push 하니 다시 변경된 모습이다. 


이번 단계에서는 <pre><code>git push --force-with-lease</code></pre>의 특징이 드러나지는 않고 일반적인 강제 푸시와 동일하게 작동하였다.


## 2번 테스트 

#### 테스트 시행 전 상황

* ##### 브랜치 목록

1. test-amend(local in desktop)
2. origin/test-amend(remote)

* ##### 브랜치별 커밋 목록 

1. test-amend(local in desktop)   
__0d0dcc20fd4b636650dcdc9d1adfc8e969bacb36__ (HEAD -> test-amend, origin/test-amend) test.html add p tag and its contents   
__bd8e25414bd55531bb0dc6864c142df944ab1e00__ README.md add 주의   
__77ede9464455dfe7646161e925d87d0f3618eb46__ README.md add accent ordered list for situations and its expectations   
__92a323f422b31f3b50d59780eeeb4c55fe0f838f__ README.md add markdowns   
__8b3c44b1fb91607c9bdc41b0512700a526fb9b1c__ README.md first commit

2. origin/test-amend(remote)

    test-amend와 동일

<hr>

#### 테스트 과정 

* in notebook

    notebook 기본 설정하고 리모트 연결하고 clone이 아닌 다른 방식으로 test-amend만 가지고 올 수 있는지 확인. 안 되면 그냥 clone 사용

    1. git pull origin test-amend 

    2. git commit --amend

    3. git push origin test-amend 

    안될 경우 

    4. git push --force-with-lease [origin test-amend]

    또 안될 경우 

    5. git push -f [origin test-amend]

<hr>

#### 테스트 결과 

##### 브랜치별 커밋 목록

1. test-amend(local in desktop)   
__014eb4c47b1af7a121d4ffe131d25dfc6c5a2d01__ (HEAD -> test-amend, origin/test-amend) test.html add p tag and its contents 수정함   
__bd8e25414bd55531bb0dc6864c142df944ab1e00__ README.md add 주의   
__77ede9464455dfe7646161e925d87d0f3618eb46__ README.md add accent ordered list for situations and its expectations   
__92a323f422b31f3b50d59780eeeb4c55fe0f838f__ README.md add markdowns   
__8b3c44b1fb91607c9bdc41b0512700a526fb9b1c__ README.md first commit

2. origin/test-amend(remote)   
local in desktop과 동일

3. test-amend(local in notebook)   
__0d0dcc20fd4b636650dcdc9d1adfc8e969bacb36__ (HEAD -> test-amend, origin/test-amend) test.html add p tag and its contents   
__bd8e25414bd55531bb0dc6864c142df944ab1e00__ README.md add 주의   
__77ede9464455dfe7646161e925d87d0f3618eb46__ README.md add accent ordered list for situations and its expectations   
__92a323f422b31f3b50d59780eeeb4c55fe0f838f__ README.md add markdowns   
__8b3c44b1fb91607c9bdc41b0512700a526fb9b1c__ README.md first commit


기존의 __0d0dcc2__ 커밋이 __014eb4c__ 커밋으로 덮어쓰여짐.

단, 1번 테스트 때와 마찬가지로 일반적인 
<pre><code>git push</code></pre>
로는 원격 저장소에 push할 수 없었다.

따라서 
<pre><code>git push --force-with-lease</code></pre>
를 사용했는데 1번 테스트 때와 마찬가지로 강제 push 되었다. 

<pre><code>git push -f</code></pre> 
또한 강제 push된다. 

다만, 이렇게 할 경우 notebook 에서 
<pre><code>git pull</code></pre>
을 하게 되면 ort 방식으로 merge 하기 때문에 역시나 원격 저장소 상에서는 사라졌었던 

__0d0dcc2__ 커밋이 재등장하게 된다. 

이 상태로 
<pre><code>git push origin test-amend</code></pre>
를 했는데 잘 적용되었다. fast-forawrd 방식으로 적용이 되었다는 뜻이다.

또 이렇게 할 경우 문제 사항은, github file history 상에서 amend한 commit인 __014eb4c__ 커밋이 아닌 기존의 __0d0dcc2__ 커밋이 최신으로 보이게 되고 __014eb4c__ 커밋은 사라져 보이지 않는다는 것이다.

이는 __git history simplification__ 때문이라고 한다. 따라서 
<pre><code>git log --full-history [file-name]</code></pre>
을 하여야 전체 history를 볼 수 있다. 

하지만 github 상에서는 여전히 볼 수 없기 때문에 다른 사용자가 이미 원격저장소의 커밋을 pull한 상태에서 강제 push를 하는 것은 위험할 수 있다. 

##### 대처(notebook에서 삭제 커밋 되돌리지 않으면서 원격저장소 pull하기)
notebook의 로컬 저장소에는 원격 저장소와 desktop의 로컬 저장소에서 삭제된(정확히 말하면 amend된) 커밋이 여전히 존재하는 상태이기 때문에 원격 저장소의 상태를 반영하여 삭제된 커밋을 로그 상에 되돌리지 않으면서 pull하는 방법이 필요하다.

지금 상태에서는 딱 한 가지 방법이 가장 적절하고 간단하다.

```
git reset --hard [previous commit of amended commit]
git pull
```

hard reset을 amended commit 바로 이전의 커밋으로 한다. 그러면 amended commit은 로그 상에서 삭제되어버린다. 그 상태에서 원격 저장소를 pull하면 알아서 fast-forward 방식으로 merge하면서 작업이 완료된다. 

이 방법이 가장 깔끔하고 적절하고 간단한 방법이다. 단 두 개의 명령어만 있으면 된다.

## 3번 테스트

#### 테스트 시행 전 상황 

* ##### 브랜치 목록 

    1. test-amend(local in desktop)
    2. origin/test-amend(remote)
    3. test-amend(local in notebook)

* ##### 브랜치별 커밋 목록 

1. test-amend(local in desktop)    
__014eb4c47b1af7a121d4ffe131d25dfc6c5a2d01__ (HEAD -> test-amend, origin/test-amend) test.html add p tag and its contents 수정함   
__bd8e25414bd55531bb0dc6864c142df944ab1e00__ README.md add 주의   
__77ede9464455dfe7646161e925d87d0f3618eb46__ README.md add accent ordered list for situations and its expectations   
__92a323f422b31f3b50d59780eeeb4c55fe0f838f__ README.md add markdowns   
__8b3c44b1fb91607c9bdc41b0512700a526fb9b1c__ README.md first commit
2. origin/test-amend    
    local in desktop과 동일
3. test-amend(local in notebook)    
    local in desktop과 동일

<hr>

#### 테스트 과정 

* in notebook

    test-amend 브랜치에서 test.html 파일 수정 -> commit.

* in desktop

    test-amend 브랜치에서 

    1. git commit --amend
    2. git push

안될 경우

    3. git push --force-with-lease

또 안될 경우

    4. git push -f

<hr>

#### 테스트 결과 

##### 브랜치별 커밋 목록

1. test-amend(local in desktop)   
__e63ab804e378eb1ded1769916d8510ffbdc69d80__ (HEAD -> test-amend, origin/test-amend) test.html add p tag and its contents 재수정함   
__bd8e25414bd55531bb0dc6864c142df944ab1e00__ README.md add 주의   
__77ede9464455dfe7646161e925d87d0f3618eb46__ README.md add accent ordered list for situations and its expectations   
__92a323f422b31f3b50d59780eeeb4c55fe0f838f__ README.md add markdowns   
__8b3c44b1fb91607c9bdc41b0512700a526fb9b1c__ README.md first commit

2. origin/test-amend(remote)   
    local in desktop과 동일

3. test-amend(local in notebook)   
__0ae37b9e293e0021d5035d6a6e0811996e41b106__ (HEAD -> test-amend) test.html add span tag and its contents   
__014eb4c47b1af7a121d4ffe131d25dfc6c5a2d01__ (origin/test-amend) test.html add p tag and its contents 수정함   
__bd8e25414bd55531bb0dc6864c142df944ab1e00__ README.md add 주의   
__77ede9464455dfe7646161e925d87d0f3618eb46__ README.md add accent ordered list for situations and its expectations   
__92a323f422b31f3b50d59780eeeb4c55fe0f838f__ README.md add markdowns   

#### 결과 설명

* in notebook

    파일을 수정하고 추가 커밋을 진행하여 __0ae37b9__ 커밋이 추가됐다. 이에 따라 원격 저장소 기준으로 앞서있게 됐다.

* in desktop

    커밋을 amend하고 push를 진행했다. 되지 않았다. 따라서 push --force-with-lease를 했다. push가 완료되었다.

* in notebook

    파일을 pull했다. 이번에는 __CONFLICT__ 가 발생하였다. 이는 __e63ab80__ 커밋이 __0ae37b9__ 커밋의 수정 내역을 반영하지 않고 있기 때문이다. 

따라서 충돌을 해결해야 했다. VS Code를 통하여 직접 파일을 수정하였다. 

여기서 크게 세 가지의 경우가 존재한다. 

1. __0ae37b9__(local in notebook) 커밋의 수정 사항만 반영
2. __e63ab80__(local in desktop) 커밋의 수정 사항만 반영
3. 둘 다 반영

물론 둘 다 반영 안 하는 경우도 있다. 하지만 그럴려면 굳이 Merge를 할 필요가 없다고 생각하기 때문에 포함하지 않았다. 또한, 두 커밋의 수정 사항을 각각 일부만 반영하고 일부는 쳐내는 경우도 포함하지 않았다. 

너무 복잡해지기 때문이다.

<hr>

__1번의 경우__

* log 상의 커밋 목록

    __3622a40a22993df81ac92a2f71e0e0e344cd37f1__ (Merge Commit) (HEAD -> test-amend) Merge branch 'test-amend' of github.com:ShangBinLee/git-commit-amend-test into test-amend   
    __e63ab804e378eb1ded1769916d8510ffbdc69d80__ (in origin/test-amend) (origin/test-amend) test.html add p tag and its contents 재수정함   
    __0ae37b9e293e0021d5035d6a6e0811996e41b106__ (since now, in test-amend) test.html add span tag and its contents   
    __014eb4c47b1af7a121d4ffe131d25dfc6c5a2d01__ test.html add p tag and its contents 수정함   
    __bd8e25414bd55531bb0dc6864c142df944ab1e00__ README.md add 주의   
    
* test.html(file) log 상의 목록 

    __0ae37b9e293e0021d5035d6a6e0811996e41b106__ (since now, in test-amend) test.html add span tag and its contents   
    __014eb4c47b1af7a121d4ffe131d25dfc6c5a2d01__ test.html add p tag and its contents 수정함   
    __e4bd2b3d1e42fb084e47ccb50c0020cd6538add3__ test.html h1 tag and content added   
    __f06d4bfac4ac8327908b80dc3581d582ab82355d__ test.html title changed   
    __36a96efacf41a34320b87bddecf9d6f71d6ad530__ test.html first commit   

log 상의 커밋 목록은 Merge 후의 일반적인 log 내용이다. Merge Commit이 최신 커밋이며, Merged Branch의 Commit도 보인다. 

하지만 test.html File log는 좀 다르다. 

<hr>

Merge Commit 생략   
__e63ab80__ 커밋 생략

__0ae37b9__ 커밋 포함(최신 커밋)

<hr>
되었다.

2번 테스트 결과에서 __history simplification__ 을 얘기했는데, 이것 때문에 File History는 전체 History와는 다르게 출력될 수 있다. 

즉, Merge Commit이 사라졌으며, __e63ab80__ 커밋(local in desktop, origin/test-amend)이 사라진 것은 모두 history simplification 때문이다.

History Simplification가 File에 대하여 적용될 때, 현재 최신 커밋(즉, HEAD가 가리키는 커밋)에서의 파일의 최종 상태를 설명할 수 있는 커밋만 보여주게 된다.

따라서 최신 커밋으로부터, 그것의 부모 커밋을 거쳐가며 커밋 간 파일의 상태(즉, Tree)를 비교하게 되고, 변경이 존재하는지 존재하지 않는지에 따라 해당 커밋을 포함할지 안 할지를 결정한다. 

자세한 내용은 현재 Repository의 

https://github.com/ShangBinLee/git-commit-amend-test/blob/master/history_simplification.md

를 참고하도록 한다. 

중요한 것은 Merge Commit이 존재할 경우, 거쳐가야 할 부모 커밋은 최소 두 개 이상이 된다는 것이며(Merge를 하더라도 Fast-Forward, Rebase 등의 방식으로 진행하면 부모 커밋은 하나 뿐이다.)
따라서, 어떤 부모 커밋을 거쳐갈지 선택해야 한다. 선택하는 부모 커밋은 최소 한 개 이상이어야 한다.(경우에 따라서는 Merge Commit의 모든 부모 커밋을 선택하는 경우도 존재한다.)

__결과적으로 Merge Commit이 존재할 경우, File History에서는 해당 File을 수정한 커밋이라고 하더라도 출력되지 않고 생략될 수 있다.__

어쨌든 이러한 History Simplification 때문에 File History 상에서 최신 커밋은 __0ae37b9__ 커밋이 된다. 

<hr>

__2번의 경우__

* log 상의 커밋 목록 

    Merge Commit 제외 
    
    1번의 경우와 동일하다.

* test.html(file) log 상의 목록

    __e63ab804e378eb1ded1769916d8510ffbdc69d80__ (origin/test-amend) test.html add p tag and its contents 재수정함   
    __e4bd2b3d1e42fb084e47ccb50c0020cd6538add3__ test.html h1 tag and content added   
    __f06d4bfac4ac8327908b80dc3581d582ab82355d__ test.html title changed   
    __36a96efacf41a34320b87bddecf9d6f71d6ad530__ test.html first commit   

log 상의 커밋 목록은 Merge Commit만 변경되었다. 이는 Commit Hash 값을 결정하는 방식 때문이다. 

test.html File log는 1번의 경우와 달라졌다. 요번에는 표시되는 총 커밋 수도 4개로 줄어들었고(분명히 -5 옵션을 사용했다.)

<hr>

Merge Commit 생략   
__0ae37b9__ 커밋 생략   
__014eb4c__ 커밋 생략

__e63ab80__ 커밋 포함

<hr>
되었다. 

여기서 1, 2번 경우에서의 공통점을 발생할 수 있다.

두 경우 모두 __Merge Commit은 생략된다.__

왜 이런 결과가 발생할까.

결과부터 말하자면, Merge Commit이 File History에 포함되지 않는 경우는, 

"__충돌이 발생한 두 커밋 중 하나의 커밋만 반영하는 경우.__"

이다. 

풀어서 말하면

1. __두 커밋 중 어떤 것도 반영하지 않는 경우__   
2. __두 커밋 모두 반영하는 경우__

에 Merge Commit이 포함된다. 

이 것은 History Simplification을 이루어내는 방식 때문이다.

어쨌든 1, 2번의 경우는 모두 두 커밋 중 하나의 커밋만 반영하는 경우이므로 Merge Commit은 포함되지 않는다. 

그렇다면 왜 각각의 경우에 Merge Commit의 부모 커밋 중 생략되는 커밋과 포함되는 커밋이 달라지는 것일까.

심지어 이번에 생략된 __014eb4c__ 커밋은 Merge Commit의 부모 커밋도 아니다.(즉, 조상 커밋이다.)

이유를 간단하게 설명하자면, 1, 2번의 경우에, Merge Commit과 같은 File 상태를 가지는(즉, File 내용의 차이가 존재하지 않는) 부모 커밋을 거쳐갈 커밋으로 선택하기 때문이다. 

그러므로 그렇지 않은 커밋은 버려진다. 

따라서 결과적으로 __0ae37b9__ 이 버려졌다. 

또한 선택된 __e63ab80__ 의 조상 커밋은 __e4bd2b3__ 커밋이다. 

즉, __014eb4c__ 커밋은 __0ae37b9__ 커밋의 부모 커밋이자 "__test-amend(local in notebook)__" 브랜치에만 존재하고 "__remotes/origin/test-amend__"에는 존재하지 않는 커밋인 것이다. 

__따라서 History Simplification은 필요할 경우 Side Branch의 커밋들을 모두 쳐내기도 한다.__

- 참고 : 여기서 Side Branch는 현재 브랜치 기준으로 병합된 브랜치를 말하는 것이 아니다. History Simplification에 의해서 거쳐가기로 선택된 커밋이 속해있는 브랜치가 아닌 나머지 브랜치들을 일컫는다. 따라서, 여기서 Side Branch는 "__test-amend(local in notebook)__"가 된다.

<hr>

__3번의 경우__

그렇다면 마지막, 모두 반영하는 3번의 경우에는 어떻게 될까. 예상을 해보자.


아마도 1, 2번에서 생략되었던 모든 커밋이 포함되어있을 것 같다. 정말 그런지 확인해보자.

* log 상의 커밋 목록 

    Merge Commit 제외 
    
    1번의 경우와 동일하다.

* test.html(file) log 상의 목록

    1번의 경우와 동일하다.

__예상과는 다르다!__

1번의 경우와 같다!

1번의 경우는 __0ae37b9__(local in notebook) 커밋의 수정 사항만 반영하는 경우였다. 그런데 이 경우와 같은 로그 내용이 출력되었다. 왜 이런 결과가 발생했을까?

정답은 요번에 Merge한 두 브랜치의 커밋 수정 내용에 있다. 

먼저 remote repository에 있었던, __e63ab80__ 커밋의 파일 내용을 보자 

    생략...
    <p>amend 테스트를 위해서 추가하는 사항</p>

그리고 local repository에 있었던, __0ae37b9__ 커밋의 파일 내용을 보자

    생략...
    <p>amend 테스트를 위해서 추가하는 사항</p>
    <span>3번 테스트를 위해서 추가하는 사항</span>

즉, __0ae37b9__ 커밋의 파일 내용은 __e63ab80__ 커밋의 파일 내용에 span 태그와 그 내용을 추가한 형태로 되어있다. 그런데 왜 Merge를 할 때 충돌이 발생했을까? 그냥 __0ae37b9__ 기준으로 병합하면 되지 않는가?

사실 이 문제는 두 커밋의 공통 조상 커밋인 __bd8e254__ 커밋의 파일 내용이 h1 태그(p 태그 바로 이전의 태그)와 그 내용까지만 가지고 있었기 때문에 발생한다. 공통 조상 기준으로 봤을 때 두 커밋은 파일의 같은 부분을 수정했고(h1 태그 이후 부분), 그 내용도 서로 다르기 때문에 충돌이 발생한 것이다. 

그렇지만 실제로 충돌을 수정할 때, 두 커밋의 수정 사항을 모두 반영하면, 파일 내용의 최종 형태는

    생략...
    <p>amend 테스트를 위해서 추가하는 사항</p>
    <span>3번 테스트를 위해서 추가하는 사항</span>

가 되기 때문에, Merge Commit에서의 파일 내용과, __0ae37b9__ 커밋의 파일 내용은 서로 같다! 즉, 두 커밋의 File 상태는 동일하다!

그러므로 두 커밋은 TREESAME 상태이다. 

결국, History는 __0ae37b9__ 커밋을 따라가게 되면서, 1번의 경우와 완전히 똑같은 File History가 나오게 되는 것이다. 



- 여기서 알 수 있는 사실은, File History에 출력되는 기준은 Merge할 당시에 해당 커밋의 수정 사항을 반영했냐, 하지 않았냐가 아니라, 순전히 Merge Commit과 TREESAME한 지를 기준으로 한다는 것이다. 따라서 Merge 당시 해당 커밋의 수정 사항을 반영했다고 하더라도 파일 내용 상 차이점이 존재한다면 상황에 따라서 File History에서 보이지 않을 수도 있다. 



원래라면, 3번의 경우까지 하고 마쳐야 하지만 3번 경우의 결과를 확인하고 나니 나머지 경우의 결과도 확인해봐야 할 것 같다. 


<hr>

__둘 다 반영하지 않는 경우__

예측은 이렇다. 

- Merge Commit 
- test-amend(local in notebook) Commit
- remotes/origin/test-amend(remote repository) Commit

모두 포함되어 출력될 것 같다. 

* log 상의 커밋 목록 

    Merge Commit 제외 
    
    1번의 경우와 동일하다.

* test.html(file) log 상의 목록

    __d2b66a01de9f3be4c3ef8006189fd9d3b06c6059__ (HEAD -> test-amend) Merge branch 'test-amend' of github.com:ShangBinLee/git-commit-amend-test into test-amend   
    __e63ab804e378eb1ded1769916d8510ffbdc69d80__ (origin/test-amend) test.html add p tag and its contents 재수정함   
    __0ae37b9e293e0021d5035d6a6e0811996e41b106__ (since now, in test-amend) test.html add span tag and its contents   
    __014eb4c47b1af7a121d4ffe131d25dfc6c5a2d01__ test.html add p tag and its contents 수정함   
    __e4bd2b3d1e42fb084e47ccb50c0020cd6538add3__ test.html h1 tag and content added   


결과는 이러하다.

- Merge Commit 포함
- __e63ab80__ 커밋 포함 
- __0ae37b9__ 커밋 포함
- __014eb4c__ 커밋 포함


3번 경우에서 __e63ab80__ 커밋과 __0ae37b9__ 커밋의 파일 내용을 보여줬다. 실제로 

```
git show e63ab80
```

을 한 경우, 

         생략...
         <h1>amend를 통한 commit 수정</h1>
    +    <p>amend 테스트를 위해서 추가하는 사항</p>    
         생략...

로 출력된다. 여기서 맨 앞에 +가 출력되어있는 줄을 보면 p 태그와 그 내용이다. 즉, __e63ab80__은 부모 커밋과 비교했을 때 p 태그와 그 내용이 추가되었다는 뜻이다. 

마찬가지로, __0ae37b9__ 커밋은

         생략...
         <h1>amend를 통한 commit 수정</h1>
         <p>amend 테스트를 위해서 추가하는 사항</p>    
    +    <span>3번 테스트를 위해서 추가하는 사항</span> 
         생략...

로 출력된다. 여기서는 부모 커밋과 비교했을 때 span 태그와 그 내용이 추가되었다. 그리고 그 부모 커밋은 __014eb4c__ 커밋이다. 즉, __e63ab80__ 커밋과 그 수정 사항과 File 상태가 같다.

그렇다면 Merge Commit은 어떨까.

          생략...
          <h1>amend를 통한 commit 수정</h1>
    --    <p>amend 테스트를 위해서 추가하는 사항</p>    
    -     <span>3번 테스트를 위해서 추가하는 사항</span> 
          생략...

으로 되어있다. 기존의 p, span 태그와 그 내용 모두 삭제한 모습이다. 

각 커밋의 File 상태를 보니 왜 File History가 저렇게 출력되었는지 추측할 수 있을 것이다. Merge Commit은 __0ae37b9__ 커밋과도, __e63ab80__ 커밋과도 TREESAME 하지 않다. 

__그렇기 때문에 Merge Commit은 그 두 커밋을 모두 거쳐가기로 결정한다.__

또한, 그렇기 때문에 그 두 커밋의 공통 조상을 만나기 전까지 두 브랜치 상에 존재하는 모든 커밋을 거쳐가야 한다. 따라서 __014eb4c__ 커밋 또한 거쳐가야 하고, 그에 따라 File History에 포함된다. 





#### History Simplification에 대한 결론
결국 History Simplification은

- 부모와 TREESAME한 커밋을 History 상에서 제외하고
- Merge Commit과 TREESAME한 부모 커밋을 따라가기 위한 목적을 가지고 있는 로그 단순화 방법이다.

## 4번 테스트

#### 테스트 시행 전 상황 

* ##### 브랜치 목록 

    1. test-amend(local in desktop)
    2. origin/test-amend(remote)
    3. test-amend(local in notebook)

* ##### 브랜치별 커밋 목록 

1. test-amend(local in desktop)    
    __0d0dcc20fd4b636650dcdc9d1adfc8e969bacb36__ (HEAD -> test-amend, origin/test-amend) test.html add p tag and its contents   
    __bd8e25414bd55531bb0dc6864c142df944ab1e00__ README.md add 주의   
    __77ede9464455dfe7646161e925d87d0f3618eb46__ README.md add accent ordered list for situations and its expectations   
    __92a323f422b31f3b50d59780eeeb4c55fe0f838f__ README.md add markdowns   
    __8b3c44b1fb91607c9bdc41b0512700a526fb9b1c__ README.md first commit
2. origin/test-amend    
    local in desktop과 동일
3. test-amend(local in notebook)    
    local in desktop과 동일  

<hr>

#### 테스트 과정 

1. in notebook

    test-amend 브랜치에서

    <pre><code>test.html 파일 수정 -> git commit -a
   git push</code></pre>

2. in desktop

    test-amend 브랜치에서 

    <pre><code>git commit --amend
   git push</code></pre>

3. 안될 경우

    <pre><code>git push --force-with-lease</code></pre>

4. 또 안될 경우

    <pre><code>git push -f</code></pre>

<hr>

#### 테스트 결과 

##### 브랜치별 커밋 목록

1. test-amend(local in desktop)   
    __4af9b58a348d817d724e41a119cb494271571634__ (HEAD -> test-amend, origin/test-amend) test.html add p tag and its contents (modify commit message of 0d0dcc2)   
    __bd8e25414bd55531bb0dc6864c142df944ab1e00__ README.md add 주의   
    __77ede9464455dfe7646161e925d87d0f3618eb46__ README.md add accent ordered list for situations and its expectations   
    __92a323f422b31f3b50d59780eeeb4c55fe0f838f__ README.md add markdowns   
    __8b3c44b1fb91607c9bdc41b0512700a526fb9b1c__ README.md first commit

2. origin/test-amend(remote)   
    local in desktop과 동일
3. test-amend(local in notebook)    
    __af39d629eea78743aaca62cdbeca12a65183d515__ (HEAD -> test-amend, origin/test-amend) test.html Add h4 tag and its content.   
    __0d0dcc20fd4b636650dcdc9d1adfc8e969bacb36__ test.html add p tag and its contents   
    __bd8e25414bd55531bb0dc6864c142df944ab1e00__ README.md add 주의   
    __77ede9464455dfe7646161e925d87d0f3618eb46__ README.md add accent ordered list for situations and its expectations   
    __92a323f422b31f3b50d59780eeeb4c55fe0f838f__ README.md add markdowns   

#### 결과 설명

1. in notebook

    파일을 수정하고 추가 커밋을 진행하여 __af39d62__ 커밋이 추가 됐다.

2. in remote repository

    notebook에서 push한 후에는 __af39d62__ 커밋이 추가 됐다. 

3. in desktop

    커밋을 amend하고 push를 진행했다. 되지 않았다. 따라서 push --force-with-lease를 했다. push가 되지 않았다. push -f를 했다. push가 완료 됐다.

4. in remote repository

    desktop에서 push한 후에는 그 전의 __af39d62__ 커밋과 __0d0dcc2__ 커밋이 사라지고 __4af9b58__ 커밋이 추가 됐다.

과정은 다를 지언정 결과적으로는 3번 테스트와 같다. 그러므로 notebook에서 pull하는 것은 생략했다. 

중요한 것은 

    git push --force-with-lease

가 요번에는 실패했다는 사실이다. 

    To github.com:ShangBinLee/git-commit-amend-test.git
    ! [rejected]        test-amend -> test-amend (stale info)
    error: failed to push some refs to 'github.com:ShangBinLee/git-commit-amend-test.git'

#### --force-with-lease 동작 방식

이것과 관련해서 이유를 알아보기 위해서 git-scm, stackoverflow, itnext.io의 문서를 참고했다. 

1. https://git-scm.com/docs/git-push#Documentation/git-push.txt---no-force-with-lease

2. https://stackoverflow.com/questions/52823692/git-push-force-with-lease-vs-force

3. https://itnext.io/git-force-vs-force-with-lease-9d0e753e8c41

4. https://git-scm.com/book/en/v2/Git-Internals-Git-References

위의 문서에 정확한 내용이 나와있지만, 쉽게 말하자면 로컬 저장소가 remote repository의 현황을 알고 있는지 확인하고 그렇다면 강제 푸시를 허용하는 명령어이다.

stackoverflow 답변에서 말하는 'remote branch on local machine' 즉, 자신의 local repository에 존재하는 현재 remote branch가 remote repository에 존재하는 branch와 최신 버전으로 연동되어 있느냐 하는 부분을 검증한다는 것이다. 

이것은 remote branch on local repository와 branch on remote repository의 ref 값을 비교함으로써 이루어진다. 만약 두 값이 동일하다면 강제 푸시는 적용된다. 

하지만, 두 값이 다를 경우 강제 푸시는 거절된다. 

그렇다면 ref값이 다른 경우와 같은 경우는 어떤 경우일까. 

먼저 로컬 저장소에서 원격 저장소로 push를 했을 때이다. 그 때는 Git에서 자동으로 ref 값을 변경한다. 

또한 fetch를 했을 때도 그러하다. 기본적으로 fetch를 하게 되면 로컬 저장소 내의 remote branch를 갱신하기 때문이다. 

따라서 fetch와 merge를 함께 진행하는 pull 명령어 또한 ref 값을 변경한다. 

결국 push, fetch, pull이 ref 값을 원격 저장소와 동기화하도록 만드는 것이다. 

다른 경우는 어떠할까? 내가 push를 해서 ref 값이 동기화된 상태인데, 다른 contributor가 다른 로컬 저장소에서 push를 했다. 그렇다면 원격 저장소의 ref 값과 나의 로컬 저장소의 ref 값은 달라지게 된다. 

결국 내가 마지막으로 원격 저장소의 branch와 동기화를 한 후, 다른 누군가가 원격 저장소에 push를 해서 원격 저장소를 변경할 경우 ref 값은 달라지고 __--force-with-lease__ 를 통한 강제 푸시는 실패하게 된다.

그렇다면 그런 경우에는 무조건 __--force__ 옵션을 사용해야 하는 걸까. 

__그렇지 않다.__

위에서 fetch를 했을 때도 ref 값을 동기화한다고 했다. 그렇다면 --force 옵션을 사용하지 않고 

    git fetch
    git push --force-with-lease

명령어를 실행하는 것은 어떨까. 

실제로 위 명령어는 성공적으로 실행된다. 

결국 force-with-lease 명령어는 강제 푸시를 수행하는 로컬 저장소의 원격 브랜치가 원격 저장소의 그것과 동기화되어 있는지를 확인하는 최소한의 안전장치다. 

##### --force-with-lease가 실패한 이유

결국, desktop에서 <code>--force-with-lease</code>를 통한 강제 푸시가 실패한 이유는, desktop에서 pull하지 않고 작업한 후 푸시 했기 때문이다. 

고로 이러한 경우, 일단 remote repository의 상태를 따로 확인하고, <code>--force(-f)</code> 옵션을 사용해서 강제 푸시를 하거나, 

    git fetch
    git push --force-with-lease

명령으로 강제 푸시 해야 한다. 