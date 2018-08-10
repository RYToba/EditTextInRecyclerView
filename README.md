# Getting EditText value In RecyclerView

RecyclerView内にてEditTextを使用しており、その値を取得したかったが少し苦戦したのでメモ(Kotlin)

</br>

## 目標

目標としては上記でも触れたが、RecyclerView内にある複数のEditTextの値をまとめて取得するということ。

(RecyclerView自体はpopupWindow内で使用し、WearableRecyclerViewを実際に使用している)

<br/>

## 解決策

Adapter内のonBindViewHolder()にて、EditTextの入力を監視するTextWatcherを使用するリスナーを登録するというもの。

自分のやりたかったこととしては、RecyclerViewにある複数のEditextの最終的な中身が取得できればよかったので最後にまとめて取得しようとしていたが、EditTextの中身が変わるたびにその値を格納していく方法で実現できるのでそちらで試した。

①インターフェースを追加する

~~~ kt
interface OnEditTextChanged {
    fun onTextChanged(position: Int, charSeq: String)
}
~~~

②onBindViewHolder()内にリスナーを登録する。

~~~ kt
class CustomAdapter(_list : ArrayList<String>, val onEditTextChanged: OnEditTextChanged): RecyclerView.Adapter<customViewHolder>() {
  .....................
  override fun onBindViewHolder(holder: customViewHolder, position: Int) {
            holder!!.textname.setText(list[position], TextView.BufferType.NORMAL)
            holder!!.linearLayout!!.id = holder.adapterPosition
            holder!!.textname.addTextChangedListener(object : TextWatcher{
                override fun afterTextChanged(s: Editable?) {
                }
                override fun beforeTextChanged(s: CharSequence?, start: Int, count: Int, after: Int) {
                }
                override fun onTextChanged(s: CharSequence?, start: Int, before: Int, count: Int) {
                    onEditTextChanged!!.onTextChanged(position, s.toString())
                }
            })
  }
~~~

③Adapterの登録時に、オーバーライドしてやる。

~~~ kt
mCustomRecyclerAdapter = CustomAdapter(Result, object:OnEditTextChanged{
            override fun onTextChanged(position: Int, charSeq: String) {
                //取得後の処理
                kResult[position] = charSeq
            }
        })
~~~

