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

#### 테스트 과정 

1. git checkout test-amend

2. git commit -amend 

3. git push origin test-amend 

#### 테스트 결과 

##### test-amend 커밋 목록 

__1f9fd3dbc212573c30874cbc788bf1df7f05403b__ (HEAD -> master, origin/master) test_process.md first commit   
__bd8e25414bd55531bb0dc6864c142df944ab1e00__ README.md add 주의   
__77ede9464455dfe7646161e925d87d0f3618eb46__ README.md add accent ordered list for situations and its expectations   
__92a323f422b31f3b50d59780eeeb4c55fe0f838f__ README.md add markdowns   
__8b3c44b1fb91607c9bdc41b0512700a526fb9b1c__ README.md first commit   

기존의 __423b89f2__ 커밋이 __1f9fd3db__ 커밋으로 덮어쓰여짐. 

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


#### git push -f & git push --force-with-lease

처음에는 

```git
git push --force-with-lease
```

를 통해 push를 해보았다. 실패하지 않고 push되어 원격 저장소에 로컬 저장소 내용을 덮어썼다. 

기존의 __423b89f29cc2b9f4720d8c067ad4f558f0795239__ 커밋이 아닌 __1f9fd3dbc212573c30874cbc788bf1df7f05403b__ 으로 변경된 모습이었다.

또다시 

```git
git push -f 
``` 

를 통해 기존의 __1f9fd3db__ 커밋을 포함하여 push 하니 다시 변경된 모습이다. 

이번 단계에서는 <pre><code>git push --force-with-lease</code></pre>의 특징이 드러나지는 않고 일반적인 강제 푸시와 동일하게 작동하였다.


## 2번 테스트 


