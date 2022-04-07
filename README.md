# Minecraft 1.12.2のMOD開発チュートリアル
MinecraftのMOD開発、情報が少なすぎる!!ということで、私がMODを作るときに学んだことを書き連ねていきます。
# 前提知識
[ここ](https://www.tntmodders.com/tutorial/#1122_Forge_142302491)の入門編を理解できていればなんとかなると思います。まずはそこを読みましょう。
# なぜ1.12.2
1.7と1.12は特に公開されているMODが多いのと、ノリです。
# 目次
* [食べ物を作ろう](https://github.com/IkedaAkihira/minecraft-1-12-2-modding-tutorials/#食べ物を作ろう)
# 食べ物を作ろう
食べられるアイテムを作るのは、比較的簡単です。  
アイテムを追加するのとほとんど同じことを行います。ただ、アイテムクラスを作成するところだけが違います。

```java:ItemFoodSample.java
//Itemの代わりにItemFoodを継承します。
public class ItemFoodSample extends ItemFood{
    //コンストラクタの記述は好みが分かれると思います。ItemFoodと同じように引数を取り、そのままsuperに与えるのもよいし、unlocalized nameなどの設定をここでは行わず、インスタンスを作った後で設定する書き方もあります。
    public ItemFoodSample(){
        //引数には、満腹度、隠し満腹度(?)、オオカミが食べるかを入力します。2番目の引数が隠し満腹度なのかはよくわかりません。
        super(1,3.0f,false);
        //アイテムと同様の記述を行います。
        this.setRegistryName("sample_mod","sample_food");
        this.setUnlocalizedName("sample_food");
        this.setCreativeTab(CreativeTabs.FOOD);

        //食べたときにポーション効果を与えることもできます。
        this.setPotionEffect(
            new PotionEffect(   //ポーション効果を与えます。
                MobEffects.INVISIBILITY,    //効果の種類です。
                20*30,  //効果の時間です。マイクラでは20tickで1秒なので、秒数*20で指定すると分かりやすいです。
                0,  //効果のレベルです。0で1,1で2と言う感じになります。
                true,   //なんか、パーティクルの量に関係するらしいです。https://www.spigotmc.org/threads/player-addpotioneffect-paramenters-ambient.259892/
                true    //パーティクルを表示するかを指定します。
            ),
            1.0f    //効果を受ける確率です。
        );

        /*
        これらのメソッドはItemFood型の返り値を持つので、以下のような記述も可能です。
        this.setRegistryName("sample_mod","sample_food")
        .setUnlocalizedName("sample_food")
        .setCreativeTab(CreativeTabs.FOOD)
        .setPotionEffect(new PotionEffect(MobEffects.INVISIBILITY,20*30,0,true,true),1.0f);
        */
    }

    //アイテムを食べたときにイベントを発生させることができます。
    //メソッドを上書きしたいとき、Intellijを使っている場合は、メソッド名を入力してエンターを押すといい感じに自動で書いてくれます。他のエディタもあるかもしれませんが。
    @Override
    protected void onFoodEaten(ItemStack stack, World worldIn, EntityPlayer player) {
        super.onFoodEaten(stack, worldIn, player);
        //createExplosionでアイテムを食べたプレイヤーの足元から爆発を起こすコードです。
        worldIn.createExplosion(
            null,   //引数名がentityInなので、多分爆破の原因を指定できるんだと思います。
            player.posX,    //爆発のx座標をプレイヤーのx座標にしています。
            player.posY-1,  //爆発のy座標をプレイヤーのy座標の1つ下にしています。
            player.posZ,    //爆発のz座標をプレイヤーのz座標にしています。
            1.0f,           //爆発の威力を指定しています。
            true            //引数名がisSmokingなので、煙の有無か何かだと思います。
        );
    }
}
```

以上!!これで、食べると透明化し、爆発する意味不明な食べ物を作ることができました。爆発の威力を8.0fとかにすると悲惨で面白いですよ。