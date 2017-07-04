---
layout: post
title: "Realm과 MVP 예제"
image: '/assets/img/'
description: '요즘 핫한 Realm과 MVP 패턴을 활용한 예제'
tags:
- Realm
- Android
- MVP
- Design Pattern
categories:
- Android
---

## Realm과 MVP Pattern을 활용한 간단한 예제
자세한 예제는 [Github](https://github.com/Jungwoon/Realm_MVP_Pattern_Practice)에서 확인할 수 있습니다.

![img](https://cdn-images-1.medium.com/max/600/1*qidJtSglIxtkk-OH3qgctg.png)
![img](https://cdn-images-1.medium.com/max/600/1*chdkcuqzHPhB5fQCnsefcA.png)
![img](https://cdn-images-1.medium.com/max/1200/1*41or-U9fBSqWWR16s_Rm6g.png)
![img](https://cdn-images-1.medium.com/max/1200/1*nMKpm96BJzoNqG95U0lZqQ.png)

### Realm 부분

#### Model을 만들어 Schema 정의
Realm에서 Schema를 지정해주는 부분 RealmObject로부터 상속을 받아서, 사용할 Schema를 지정해줍니다.

```java

public class Member extends RealmObject {
    @PrimaryKey
    private int index;
    private String name;
    private String department;
    private String title;
    private int salary;

    public int getIndex() {
        return index;
    }

    public void setIndex(int index) {
        this.index = index;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getDepartment() {
        return department;
    }

    public void setDepartment(String department) {
        this.department = department;
    }

    public String getTitle() {
        return title;
    }

    public void setTitle(String title) {
        this.title = title;
    }

    public int getSalary() {
        return salary;
    }

    public void setSalary(int salary) {
        this.salary = salary;
    }
}

```

#### DBHelper를 통해 입력, 수정 및 삭제
DBHelper를 Singleton 으로 구성해서 좀 더 쉽게 접근하게끔 만들어 줌

```java

public class DBHelper {
    private static DBHelper ourInstance = new DBHelper();

    public static DBHelper getInstance() {
        return ourInstance;
    }

    Realm realm;

    public DBHelper() {
        realm = Realm.getDefaultInstance();
    }

    public Realm getRealmInstance() {
        return realm;
    }

    // 새로 추가하는 부분
    public void insertMember(String name, String department, String title, int salary) {
        Member member = new Member();

        member.setIndex(getAutoIncrementIndex(member));
        member.setName(name);
        member.setDepartment(department);
        member.setTitle(title);
        member.setSalary(salary);

        realm.beginTransaction();
        realm.copyToRealm(member);
        realm.commitTransaction();

    }

    // 현제 테이블에 있는 모든 Member를 리스트로 받는 부분
    public ArrayList<MemberItem> getMembers() {
        RealmResults<Member> members = realm.where(Member.class).findAll().sort("index", Sort.DESCENDING);
        ArrayList<MemberItem> memberItems = new ArrayList<>();

        // 질의한 결과를 RecyclerView에서 이용할 수 있도록 arrayList에 넣어주는 부분
        for (int i = 0; i < members.size(); i++) {
            memberItems.add(new MemberItem(
                    members.get(i).getIndex(),
                    members.get(i).getName(),
                    members.get(i).getDepartment(),
                    members.get(i).getTitle(),
                    members.get(i).getSalary()
            ));
        }

        return memberItems;
    }

    public RealmResults<Member> getMemberWithIndex(int index) {
        return realm.where(Member.class).equalTo("index", index).findAll();
    }

    // index에 해당하는 정보를 변경
    public void editMemberWithIndex(int index, String name, String department, String title, int salary) {
        RealmResults<Member> result = getMemberWithIndex(index);

        if (result.isEmpty()) {
            return;
        }

        Member member = result.get(0);

        realm.beginTransaction();
        member.setName(name);
        member.setDepartment(department);
        member.setTitle(title);
        member.setSalary(salary);
        realm.commitTransaction();

    }

    // index 값을 받아서 해당하는 member를 삭제
    public void deleteMemberWithIndex(int index) {
        RealmResults<Member> result = getMemberWithIndex(index);

        if (result.isEmpty()) {
            return;
        }

        realm.beginTransaction();
        result.deleteAllFromRealm();
        realm.commitTransaction();

    }

    public int getNewMemberIndex() {
        return getAutoIncrementIndex(new Member());
    }

    // Realm에 Autoincrement가 없기 때문에 수동으로 처리해주는 부분
    private int getAutoIncrementIndex(Object object) {
        int nextIndex;
        Number currentIndex = null;

        // 질의 부분에서 instance를 확인하여 들어가는 부분
        // index에서 가장 큰 값을 구해서, 1을 더해준다.
        if (object instanceof Member) {
            currentIndex = realm.where(Member.class).max("index");
        }

        if (currentIndex == null) {
            nextIndex = 0;
        }
        else {
            nextIndex = currentIndex.intValue() + 1;
        }

        return nextIndex;
    }

}

```
#### Tips
Realm에서는 Auto Increment가 없기 때문에 아래 소스와 같이 별도의 함수를 만들어서 처리
해당 모델의 인스턴스를 확인한 후 그걸 이용해서 가장 큰 index를 받아와 1을 더해서 반환

```java
// Realm에 Auto Increment가 없기 때문에 수동으로 처리해주는 부분
private int getAutoIncrementIndex(Object object) {
    int nextIndex;
    Number currentIndex = null;

    // 질의 부분에서 instance를 확인하여 들어가는 부분
    // index에서 가장 큰 값을 구해서, 1을 더해준다.
    if (object instanceof Member) {
        currentIndex = realm.where(Member.class).max("index");
    }

    if (currentIndex == null) {
        nextIndex = 0;
    }
    else {
        nextIndex = currentIndex.intValue() + 1;
    }

    return nextIndex;
}
```

### MVP Pattern
최근에 Design Pattern에 관심을 가지면서, 많이 부족하지만 꿈 많은 개발자로 활동하시는 김태환님의 [블로그](http://thdev.tech/androiddev/2016/10/12/Android-MVP-Intro.html)를 참고하여 만들었습니다.

#### 구성
![img](https://cdn-images-1.medium.com/max/1200/1*Gy9MEiv55PrZK3Jf6kx93w.png)

#### MainContract
메인이 되는 Contract 부분을 Interface로 정의하여, Presenter와 MainActivity에서 implements 받아 사용합니다.

```java

public interface MainContract {

    interface View {

        void showToast(int index);
    }

    interface Presenter {

        void setMyAdapterView(MyAdapterContract.View adapterView);

        void setMyAdapterModel(MyAdapterContract.Model adapterModel);

        void attachView(View view);

        void detachView();

        void loadItems(Context context, boolean isClear);

    }
}

```

#### MainPresenter
중간에서 모든 역할을 다 하는 Presenter를 정의해주는 소스, View에서 요청을 받아, Model로부터 데이터를 받아 처리하고 다시 View에 뿌려주는 역할을 합니다.

```java

public class MainPresenter implements MainContract.Presenter, OnItemClickListener {

    private MainContract.View view;
    private MyAdapterContract.View adapterView;
    private MyAdapterContract.Model adapterModel;

    @Override
    public void setMyAdapterView(MyAdapterContract.View adapterView) {
        this.adapterView = adapterView;

        this.adapterView.setOnClickListener(this);
    }

    @Override
    public void setMyAdapterModel(MyAdapterContract.Model adapterModel) {
        this.adapterModel = adapterModel;
    }

    @Override
    public void attachView(MainContract.View view) {
        this.view = view;
    }

    @Override
    public void detachView() {
        view = null;
    }

    @Override
    public void loadItems(Context context, boolean isClear) {
        // 이미
        ArrayList<MemberItem> memberItems = DBHelper.getInstance().getMembers();

        if (isClear) {
            adapterModel.clearItems();
        }

        adapterModel.addItems(memberItems);
        adapterView.notifyAdapter();
    }

    @Override
    public void onItemClick(int position) {
        MemberItem memberItem = adapterModel.getItem(position);
        view.showToast(memberItem.getIndex());
    }
}

```

#### AdapterContract
Adapter 자체도 별도의 MVP패턴으로 처리해주기 위해서 AdapterContract을 정의하여 사용

```java

public interface MyAdapterContract {

    interface View {

        void setOnClickListener(OnItemClickListener onClickListener);

        void notifyAdapter();

    }

    interface Model {

        void addItems(ArrayList<MemberItem> memberItems);

        void clearItems();

        MemberItem getItem(int position);
    }
}

```

자세한 소스는 위쪽에 정의한 링크를 확인하면 전체 소스를 확인할 수 있습니다.
관련해서, 잘못된 부분이나 틀린 부분있으면 Comment 달아주시면 수정하도록 하겠습니다.
