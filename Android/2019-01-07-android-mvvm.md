# [안드로이드] 안드로이드 MVVM에 대해 알아보자1

- Title : [안드로이드] 안드로이드 MVVM에 대해 알아보자1
- Date : 2019-01-07
- Category: Android

Coding in Flow의 Room + ViewModel + LiveData + RecyclerView (MVVM) 튜토리얼을 정리했습니다.

# 파일 구성

- MainActivity.java
- Note.java
- NoteAdapter.java
- NoteDao.interface
- NoteDatabase.java
- NoteRepository.java
- NoteViewModel.java

# MVVM - VIEW와 VIEWMODEL

<span class="li-icon">MainActivity.class : 앱의 시작점으로 RecyclerView 설정 및 ViewModel 생성하는 부분 </span>
<span class="li-icon">NoteViewModel.class : UI 컴포넌트와 UI 데이터의 분리</span>
<span class="li-icon">NoteAdapter.class : 리사이클러뷰 어댑터</span>
<span class="li-icon">NoteRepository.class : 저장소, 도메인과 모델 사이의 중간체 역할?</span>

## 1. 리사이클러뷰 구현

```java
RecyclerView recyclerView = findViewById(R.id.recycler_view);
recyclerView.setLayoutManager(new LinearLayoutManager(this));   // 1)
recyclerView.setHasFixedSize(true); // 2)

final NoteAdapter adapter = new NoteAdapter();  // 3)
recyclerView.setAdapter(adapter);   // 3-1)
```

1. LayoutManager 클래스, 아이템을 어떻게 배치할 것인가를 결정한다.  
   <span class="clr-grey">LinearLayoutManager 클래스 : 가로/세로 형태로 아이템을 배열한다.</span>

2. 각 아이템의 변화가 리사이클러 뷰의 전체 크기에 영향을 끼치지 않는다면 true를 사용한다.

3. 데이터를 리사이클러뷰에 연결해 줄 어댑터 객체 생성

3-1) 3)에서 생성한 어댑터 객체를 리사이클러뷰에 붙인다.

<span id="viewModel"></span>

## 2. ViewModel과 LiveData

[뷰모델 개념 익히러가기](/android/android-viewModel/).

```java
noteViewModel = ViewModelProviders.of(this).get(NoteViewModel.class);
```

**ViewModel 객체 요청**  
보통 ViewModel 생성 요청은 <code class="codetainer">onCreate()</code> 메서드에서 이루어지며, **ViewModelProvider 클래스**를 통해서 ViewModel 객체를 생성한다.
ViewModelProviders 클래스의 <code class="codetainer">of(this)</code> 를 통해 this(UI 컨트롤러) 스코프를 가진 ViewModelProvider 인스턴스가 반환된다.
리턴된 ViewModelProvider 객체의 메서드 <code class="codetainer">get(NoteViewModel.class)</code> 를 통해 NoteViewModel 인스턴스가 생성된다.

<span class="clr-grey">**NOTE:** get()은 이미 ViewModel 객체가 생성되었을 경우, 그 객체를 리턴한다. </span>

<span id="LiveData"></span>
**LiveData 사용**  
[LiveData 개념 익히러가기](/android/android-LiveData/).

noteViewModel.java

```java
public class NoteViewModel extends AndroidViewModel {
    // ...생략
    private LiveData<List<Note>> allNotes;

    public NoteViewModel(@NonNull Application application) {
        super(application);
        repository = new NoteRepository(application);
        allNotes = repository.getAllNotes();
    }

    // ...생략
    public LiveData<List<Note>> getAllNotes() {
        return allNotes;
    }
}
```

LiveData는 보통 ViewModel 내에서 함께 쓰인다.

MainActivity.java

```java
noteViewModel.getAllNotes().observe(this, new Observer<List<Note>>() {  // 1) LifeCycleOwner, Observer
    @Override
    public void onChanged(@Nullable List<Note> notes) { // 2)
        // update RecyclerView
        // Toast.makeText(MainActivity.this, "onchanged", Toast.LENGTH_SHORT).show();
        adapter.setNotes(notes);
    }
});
```

1. <code class="codetainer">noteViewModel.getAllNotes()</code>를 통해 LiveData를 리턴받고 <code class="codetainer">observe()</code> 메서드를 통해 Observer를 붙여 감지를 시작한다.
   <span class="clr-grey">보통 Observer는 프래그먼트나 액티비티 같은 UI 컨트롤러에서 만든다.</span>  
   <span class="clr-note">LiveData의 data가 변경될 때 LifeCycleOwner가 활성화 되어있는 한 등록된 모든 Observer들 에게 이벤트를 보낸다.</span>

2. Observer는 <code class="codetainer">onChanged()</code> 메서드를 반드시 오버라이드해야하며, <code class="codetainer">onChanged()</code> 메서드에서 UI 업데이트를 한다.

# MVVM - MODEL

<span class="li-icon">NoteDatabase.class</span>
<span class="li-icon">NoteDao.interface</span>
<span class="li-icon">Note.class</span>

Room은 SQLite 추상계층을 감싸고 있으며, 쉽게 데이터베이스에 접근하여 SQLite를 자유롭게 사용할 수 있다. Room에는 세가지 주요한 컴포넌트가 있다.

## 1. Database (NoteDatabase.class)

데이터베이스 홀더를 포함하고, 관계형 데이터베이스에 접근할 수 있는 액세스 포인트를 제공한다.

```java
@Database(entities = {Note.class}, version = 1, exportSchema = false)   // 1)
public abstract class NoteDatabase extends RoomDatabase {

    private static NoteDatabase instance;

    public abstract NoteDao noteDao();  // 3)

    public static synchronized NoteDatabase getInstance(Context context) {
        if (instance == null) {
            instance = Room.databaseBuilder(context.getApplicationContext(),    // 4)
                    NoteDatabase.class, "note_database")
                    .fallbackToDestructiveMigration()
                    .addCallback(roomCallback)
                    .build();
        }
        return instance;
    }
    // ..생략
}
```

1. @Database 애노테이션을 클래스에 달아야하며, 데이터베이스와 관련된 Entity들은 애노테이션 인자값으로 포함해야한다.
2. RoomDatabase를 상속한 abstract class여야 한다.
3. abstract method 포함해야하는데, 이 메소드에는 인자가 0개이고 reture되는 클래스가 @Dao 애노테이션을 달고 있어야한다.
4. 런타임때에는 Room.databaseBuilder() 또는 Room.inMemoryDatabaseBuilder()를 통해 Database의 객체를 얻어 낼 수 있다.

<span class="clr-grey">**Note:** RoomDatabase 객체를 인스턴스화 하는 비용은 매우 크므로 인스턴스를 얻는 작업을 싱글톤패턴으로 만드는 게 좋다.</span>

```java
@Database(entities = {Note.class}, version = 1, exportSchema = false)   // 1)
public abstract class NoteDatabase extends RoomDatabase {
    private static NoteDatabase instance;
    public abstract NoteDao noteDao();
    // ..생략

    private static RoomDatabase.Callback roomCallback = new RoomDatabase.Callback() {

        // 데이터베이스가 처음 생성될 때 호출됨, 모든 테이블이 생성된 후 호출됨.
        @Override
        public void onCreate(@NonNull SupportSQLiteDatabase db) {
            super.onCreate(db);
            new PopulateDbAsyncTask(instance).execute(); // AsyncTask 실행
        }
    };

    private static class PopulateDbAsyncTask extends AsyncTask<Void, Void, Void> {
        private NoteDao noteDao;

        private PopulateDbAsyncTask(NoteDatabase db) {
            noteDao = db.noteDao();
        }

        @Override
        protected Void doInBackground(Void... voids) {
            noteDao.insert(new Note("Title 1", "Description 1", 1));
            noteDao.insert(new Note("Title 2", "Description 2", 2));
            noteDao.insert(new Note("Title 3", "Description 3", 3));

            return null;
        }
    }
}
```

**AsyncTask클래스**  
비즈니스 로직과 UI 컴포넌트 조작이 동시에 일어나야 할 때 유용하며, 비교적 오래 걸리지 않는 작업에 적합하다. 또한, task 캔슬이 용이하다.

AsyncTask <Params, Progress, Result> 제너릭 타입
<span class="li-icon">Params: doInBackground 파라미타 타입이며, execute 메소드의 인자 값이 된다.</span>
<span class="li-icon">Progress: doInBakcgroud 작업 시 진행 단위의 타입으로 onProgressUpdate 파라미터의 타입이다.</span>
<span class="li-icon">doInBackground 리턴값으로 onPostExecute 파라미터 타입이다.</span>

<span class="clr-grey">**제네릭스(Generics):** 객체 생성시 타입을 선언하므로 캐스팅할 필요가 없으며, 다른 타입을 할당할 경우 컴파일 단계에서 예외처리가 된다. <T>는 객체(Object) 타입이다.</span>

## 2. Entity (Note.class)

Entity를 사용하여 데이터 구조를 정의하고, 데이터베이스 테이블을 표현한다.

```java
@Entity(tableName = "note_table")   // tableName 속성 : 테이블명을 Note로 사용하고 싶지 않을 때
public class Note {

    @PrimaryKey(autoGenerate = true)    // 기본키 정의 필수
    private int id;
    @ColumnInfo(name = "Notetitle") // name 속성 : 필드명을 다르게 지정하고 싶을 때
    private String title;
    private String description;
    private int priority;
    @Ignore // 데이터베이스에서 칼럼으로 생성되기를 원치 않을 때
    Bitmap picture;

    public Note(String title, String description, int priority) {
        this.title = title;
        this.description = description;
        this.priority = priority;
    }

    // ..생략 (반드시, getter setter 필요)
}
```

## 3. DAO (NoteDao.interface)

데이터베이스의 데이터에 접근하기 위해서는 DAO가 필요하다. 직접적인 쿼리를 작성하는 대신 DAO 클래스를 사용하여 데이터베이스에 추상적으로 접근한다. <span class="clr-note">DAO는 interface나 abstract class가 되야한다.</span>

```java
@Dao
public interface NoteDao {

    @Insert
    void insert(Note note);

    @Update
    void update(Note note);

    @Delete
    void delete(Note note);

    @Query("DELETE FROM note_table")
    void deleteAllNotes();

    @Query("SELECT * FROM note_table ORDER BY priority DESC")
    LiveData<List<Note>> getAllNotes();
}
```
