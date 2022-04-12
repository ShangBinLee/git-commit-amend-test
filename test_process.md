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

##### test-amend 커밋 목록 


__0d0dcc20fd4b636650dcdc9d1adfc8e969bacb36__ (HEAD -> master, origin/master) test.html add p tag and its contents   
__bd8e25414bd55531bb0dc6864c142df944ab1e00__ README.md add 주의   
__77ede9464455dfe7646161e925d87d0f3618eb46__ README.md add accent ordered list for situations and its expectations   
__92a323f422b31f3b50d59780eeeb4c55fe0f838f__ README.md add markdowns   
__8b3c44b1fb91607c9bdc41b0512700a526fb9b1c__ README.md first commit   

기존의 __423b89f2__ 커밋이 __0d0dcc20__ 커밋으로 덮어쓰여짐. 


git push origin test-amend를 진행했을 때 에러가 발생. 

에러 내용 

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

#### git push -f & git push --force-with-lease

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
__0d0dcc20fd4b636650dcdc9d1adfc8e969bacb36__ (HEAD -> master, origin/master) test.html add p tag and its contents 
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
__0d0dcc20fd4b636650dcdc9d1adfc8e969bacb36__ (HEAD -> master, origin/master) test.html add p tag and its contents   
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