[deepseek_html_20260920_660dad.html](https://github.com/user-attachments/files/32436586/deepseek_html_20260920_660dad.html)
<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>مشغل الراديو والأغاني</title>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; font-family: 'Segoe UI', Tahoma, sans-serif; }
        
        body {
            background: #000;
            color: #fff;
            min-height: 100vh;
            background-image: url('https://images.unsplash.com/photo-1590602847861-f357a9332bbc?q=80&w=2070&auto=format&fit=crop');
            background-size: cover;
            background-position: center;
            background-blend-mode: overlay;
            padding: 20px;
            padding-bottom: 90px;
        }

        .overlay {
            position: fixed; top: 0; left: 0; width: 100%; height: 100%;
            background: rgba(0, 0, 0, 0.88); z-index: 0;
        }

        .container {
            position: relative; z-index: 1;
            max-width: 1100px;
            margin: 0 auto;
        }

        .header {
            text-align: center;
            margin-bottom: 30px;
        }
        .logo {
            font-size: 2.5rem; font-weight: bold; color: #fff;
            text-shadow: 0 0 20px rgba(255, 102, 0, 0.5);
        }
        .logo span { color: #ff6600; }
        .subtitle { font-size: 0.9rem; color: #888; margin-top: 5px; }

        .category {
            margin-bottom: 25px;
            background: rgba(255, 255, 255, 0.03);
            border-radius: 12px;
            padding: 15px;
            border: 1px solid rgba(255, 255, 255, 0.08);
        }

        .category-title {
            font-size: 1.1rem;
            color: #ff6600;
            margin-bottom: 12px;
            padding-bottom: 8px;
            border-bottom: 1px solid rgba(255, 102, 0, 0.3);
        }

        .buttons-grid {
            display: flex;
            flex-wrap: wrap;
            gap: 8px;
        }

        .station-btn {
            background: rgba(255, 255, 255, 0.06);
            border: 1px solid rgba(255, 102, 0, 0.4);
            color: #fff;
            padding: 8px 14px;
            border-radius: 20px;
            cursor: pointer;
            font-size: 0.85rem;
            transition: all 0.3s;
            white-space: nowrap;
        }

        .station-btn:hover {
            background: #ff6600;
            color: #000;
            transform: translateY(-2px);
            box-shadow: 0 4px 10px rgba(255, 102, 0, 0.4);
        }

        .station-btn.active {
            background: #ff6600;
            color: #000;
            box-shadow: 0 0 15px rgba(255, 102, 0, 0.6);
        }

        .player-bar {
            position: fixed;
            bottom: 0; left: 0; right: 0;
            background: rgba(0, 0, 0, 0.95);
            backdrop-filter: blur(15px);
            border-top: 1px solid rgba(255, 102, 0, 0.3);
            padding: 12px 20px;
            display: flex;
            align-items: center;
            justify-content: space-between;
            z-index: 100;
            gap: 15px;
        }

        .now-playing {
            flex: 1;
            font-size: 0.9rem;
            color: #ff6600;
            overflow: hidden;
            text-overflow: ellipsis;
            white-space: nowrap;
        }

        .controls {
            display: flex;
            gap: 15px;
            align-items: center;
        }

        .controls button {
            background: none; border: none; color: #fff;
            font-size: 1.5rem; cursor: pointer; transition: color 0.3s;
        }
        .controls button:hover { color: #ff6600; }

        audio { display: none; }

        ::-webkit-scrollbar { width: 8px; }
        ::-webkit-scrollbar-track { background: #111; }
        ::-webkit-scrollbar-thumb { background: #ff6600; border-radius: 4px; }
    </style>
</head>
<body>
    <div class="overlay"></div>

    <div class="container">
        <div class="header">
            <div class="logo">Radio <span>F.M</span></div>
            <div class="subtitle">جميع المحطات والأغاني - مفروزة حسب الفئة</div>
        </div>

        <div id="categoriesContainer"></div>
    </div>

    <div class="player-bar">
        <div class="now-playing" id="nowPlaying">🎵 اختر أغنية أو محطة للبدء</div>
        <div class="controls">
            <button onclick="prevTrack()">⏮</button>
            <button onclick="playPause()">▶</button>
            <button onclick="stopTrack()">⏸</button>
            <button onclick="nextTrack()">⏭</button>
        </div>
    </div>

    <audio id="audioPlayer"></audio>

    <script>
        // ============================================================
        // ضع هنا جميع الروابط مفروزة حسب الفئة
        // ============================================================
        const allData = [
            // ===================== الراديو =====================
            {
                category: "📻 محطات الراديو",
                items: [
                    { name: "راديو القرآن 1", url: "http://live.mp3quran.net:9726/" },
                    { name: "راديو القرآن 2", url: "http://live.mp3quran.net:9988/" },
                    { name: "فيروز FM", url: "https://sawtelghad.org:8008/;" },
                    { name: "شام FM", url: "http://radioshamfm.grtvstream.com:8400/.mp3" },
                    { name: "أم كلثوم FM", url: "https://stream.zeno.fm/puv30d6l1bjtv" },
                    { name: "فرح FM", url: "https://radio.farah.fm/?type=http" },
                    { name: "نغم FM", url: "https://ahmsamir.radioca.st/stream" },
                    { name: "المدينة FM", url: "https://generationfm-underground.ice.infomaniak.ch/generationfm-underground-high.mp3" },
                    { name: "روزانا FM", url: "https://generationfm-underground.ice.infomaniak.ch/generationfm-underground-high.mp3" },
                    { name: "الرجال FM", url: "https://stream-57.zeno.fm/ebitxokyr7ptv" },
                    { name: "راديو التسعينات", url: "https://eu1.fastcast4u.com/proxy/prontofm?mp=%2F1" },
                    { name: "عبد الحليم FM", url: "https://stream.zeno.fm/8a4eqkd0pnhvv" },
                    { name: "راديو طرب", url: "https://stream.zeno.fm/4nnehdnvpg8uv" },
                    { name: "راديو الغد", url: "http://s10.voscast.com:9410/live" },
                    { name: "ملحم بركات FM", url: "https://stream.zeno.fm/gwhk3u1keuhvv.mp3" },
                    { name: "ميلودي FM", url: "https://stream.zeno.fm/3gkedytbapbuv" },
                    { name: "أهلا زمان", url: "https://stream.zeno.fm/tb47xcqv1zhvv" },
                    { name: "رحبانيات FM", url: "https://stream.zeno.fm/ctmapun6ot5vv" },
                    { name: "Oriental FM", url: "https://rautemusik.stream25.radiohost.de/oriental" },
                    { name: "SunShine FM", url: "http://stream.sunshine-live.de/live/mp3-192/liveonlineradio/?type=.mp3" }
                ]
            },

            // ===================== فيروز =====================
            {
                category: "🎤 فيروز",
                items: [
                    { name: "سمره يا ام عيون وساع", url: "https://dl.nghmaty.com/s1/78PR36D0.mp3" },
                    { name: "كتير محلاية", url: "https://dl.nghmaty.com/s1/23Cr77Fv.mp3" },
                    { name: "سألتك حبيبي", url: "https://serv100.albumaty.com/dl/feh/fairouz/singels/Saaltk_Habiby.mp3" },
                    { name: "شو قوليك", url: "https://dl.nghmaty.com/s1/85E396Vk.mp3" },
                    { name: "أيا حجل", url: "https://dl.nghmaty.com/s1/26up744E.mp3" },
                    { name: "الله معك ياهوانا", url: "https://serv100.albumaty.com/dl/feh/fairouz/singels/Alah_Maak_Ya_Hawana.mp3" },
                    { name: "بقطفلك بس", url: "https://serv100.albumaty.com/dl/feh/fairouz/singels/Baqtoflak_Bas.mp3" },
                    { name: "بكتب اسمك", url: "https://serv100.albumaty.com/dl/feh/fairoz/Bakteb_Esmak.mp3" },
                    { name: "حبيتك بالصيف", url: "https://serv100.albumaty.com/dl/feh/fairouz/singels/Habytak_Bl_Saif.mp3" },
                    { name: "نحنا والقمر جيران", url: "https://serv100.albumaty.com/dl/feh/fairouz/singels/Nahn_Wa_Al_Qamar_Geran.mp3" },
                    { name: "علي جسر اللوزية", url: "https://serv100.albumaty.com/dl/feh/fairouz/singels/Ala_Gesr_Al_Wzya.mp3" },
                    { name: "قديش كان فى ناس", url: "https://serv100.albumaty.com/dl/feh/fairouz/singels/Qadeesh_Kan_Fee_Nas.mp3" },
                    { name: "لا انت حبيبي", url: "https://serv100.albumaty.com/dl/feh/fairouz/singels/La_Enta_Habiby.mp3" },
                    { name: "فتحهن على", url: "https://serv100.albumaty.com/dl/feh/fairouz/singels/Ftahhn_Ala.mp3" },
                    { name: "كيفك انت", url: "https://serv100.albumaty.com/dl/feh/fairouz/singels/Keyfak_Enta.mp3" },
                    { name: "قالوا العدا", url: "https://serv100.albumaty.com/dl/feh/fairouz/singels/Qalo_Alada.mp3" },
                    { name: "ليلية بترجع ياليل", url: "https://serv100.albumaty.com/dl/feh/fairouz/singels/Leila_Btergaa_Ya_Leil.mp3" },
                    { name: "نسم علينا الهوى", url: "https://serv100.albumaty.com/dl/feh/fairoz/Nesm_Alyna_ElHawa.mp3" },
                    { name: "سهر الليالى", url: "https://serv100.albumaty.com/dl/feh/fairouz/singels/Sahar_El_Layali.mp3" },
                    { name: "اغنية بيت صغير", url: "https://serv100.albumaty.com/dl/feh/fairoz/albums/bebale/10_Baytee_Zgheer.mp3" },
                    { name: "انا لحبيبى", url: "https://serv100.albumaty.com/dl/feh/fairoz/Ana_Le_Habiby.mp3" }
                ]
            },

            // ===================== أم كلثوم =====================
            {
                category: "🎧 أم كلثوم",
                items: [
                    { name: "الف ليلة وليلة", url: "https://serv100.albumaty.com/dl/alf/om-kalsoum/Albumaty.com.Om.Kalsoum.Alf.Lela.W.Lela.mp3" },
                    { name: "فكروني", url: "https://serv100.albumaty.com/dl/alf/om-kalsoum/Albumaty.com.Om.Kalsoum.Fakrony.mp3" },
                    { name: "بعيد عنك", url: "https://serv100.albumaty.com/dl/alf/om-kalsoum/Albumaty.com.Om.Kalsoum.B3ed.3nk.mp3" },
                    { name: "سيرة الحب", url: "https://serv100.albumaty.com/dl/alf/om-kalsoum/Albumaty.com.Om.Kalsoum.Sert.El7ob.mp3" },
                    { name: "يا مسهرني", url: "https://serv100.albumaty.com/dl/alf/om-kalsoum/Albumaty.com.Om.Kalsoum.Ya.Mshrne.mp3" },
                    { name: "عودت عيني", url: "https://serv100.albumaty.com/dl/alf/om-kalsoum/Albumaty.com.Om.Kalsoum.3awedt.3eny.mp3" },
                    { name: "فات الميعاد", url: "https://serv100.albumaty.com/dl/alf/om-kalsoum/Albumaty.com.Om.Kalsoum.Fat.Elm3ad.mp3" },
                    { name: "انما للصبر حدود", url: "https://serv100.albumaty.com/dl/alf/om-kalsoum/Albumaty.com.Om.Kalsoum.Llsbr.7dod.mp3" },
                    { name: "ليلة حب", url: "https://serv100.albumaty.com/dl/alf/om-kalsoum/Albumaty.com.Om.Kalsoum.Lylt.7ob.mp3" },
                    { name: "الحب كله", url: "https://serv100.albumaty.com/dl/alf/om-kalsoum/Albumaty.com.Om.Kalsoum.El7ob.Kolo.mp3" },
                    { name: "امل حياتي", url: "https://serv100.albumaty.com/dl/alf/om-kalsoum/Albumaty.com.Om.Kalsoum.Amal.7yaty.mp3" },
                    { name: "الاطلال", url: "https://serv100.albumaty.com/dl/alf/om-kalsoum/Albumaty.com.Om.Kalsoum.Elatlal.mp3" },
                    { name: "اغداً ألقاك", url: "https://serv100.albumaty.com/dl/alf/om-kalsoum/Albumaty.com.Om.Kalsoum.Aghdn.Alqak.mp3" },
                    { name: "اسال روحك", url: "https://serv100.albumaty.com/dl/alf/om-kalsoum/Albumaty.com.Om.Kalsoum.Es2l.Ro7k.mp3" },
                    { name: "ومرت الايام", url: "https://serv100.albumaty.com/dl/alf/om-kalsoum/Albumaty.com.Om.Kalsoum.Wmrt.El.Ayam.mp3" },
                    { name: "حسيبك للزمن", url: "https://serv100.albumaty.com/dl/alf/om-kalsoum/Albumaty.com.Om.Kalsoum.7sebk.Llzmn.mp3" },
                    { name: "بتفكر في مين", url: "https://serv100.albumaty.com/dl/alf/om-kalsoum/Albumaty.com.Om.Kalsoum.Btfkr.Fe.Meen.mp3" },
                    { name: "ظلمنا الحب", url: "https://serv100.albumaty.com/dl/alf/om-kalsoum/Albumaty.com.Om.Kalsoum.Zlmna.El7ob.mp3" },
                    { name: "حكم علينا الهوى", url: "https://serv100.albumaty.com/dl/alf/om-kalsoum/Albumaty.com.Om.Kalsoum.7km.3alena.El.Hawa.mp3" },
                    { name: "هجرتك", url: "https://serv100.albumaty.com/dl/alf/om-kalsoum/Albumaty.com.Om.Kalsoum.Hgrtk.mp3" },
                    { name: "من اجل عينيك", url: "https://serv100.albumaty.com/dl/alf/om-kalsoum/Albumaty.com.Om.Kalsoum.Mn.Agl.3nek.mp3" },
                    { name: "هو صحيح الهوى غلاب", url: "https://serv100.albumaty.com/dl/alf/om-kalsoum/Albumaty.com.Om.Kalsoum.Howa.Sa7e7.El.Hawa.3lab.mp3" },
                    { name: "حب ايه", url: "https://serv100.albumaty.com/dl/alf/om-kalsoum/Albumaty.com.Om.Kalsoum.7ob.Eh.mp3" },
                    { name: "اروح لمين", url: "https://serv100.albumaty.com/dl/alf/om-kalsoum/Albumaty.com.Om.Kalsoum.Aro7.Lmeen.mp3" },
                    { name: "انت عمري", url: "https://serv100.albumaty.com/dl/alf/om-kalsoum/Albumaty.com.Om.Kalsoum.Enta.3omry.mp3" },
                    { name: "دليلي أحتار", url: "https://serv100.albumaty.com/dl/alf/om-kalsoum/Albumaty.com.Om.Kalsoum.Dalely.E7tar.mp3" },
                    { name: "رباعيات الخيام", url: "https://serv100.albumaty.com/dl/alf/om-kalsoum/Albumaty.com.Om.Kalsoum.Roba3yat.Elkhyam.mp3" },
                    { name: "للصبر حدود", url: "https://serv100.albumaty.com/dl/alf/om-kalsoum/Albumaty.com.Om.Kalsoum.Llsbr.7dod.mp3" },
                    { name: "انا في انتظارك", url: "https://serv100.albumaty.com/dl/alf/om-kalsoum/Albumaty.com.Om.Kalsoum.Ana.Fe.Intzark.MP3" },
                    { name: "يعشق كل جميل", url: "https://serv100.albumaty.com/dl/alf/om-kalsoum/Albumaty.com.Om.Kalsoum.Elqlb.Y3shq.Kol.Gmel.mp3" },
                    { name: "الاوله في الغرام", url: "https://serv100.albumaty.com/dl/alf/om-kalsoum/Albumaty.com.Om.Kalsoum.Elawla.Fe.Elghram.mp3" },
                    { name: "اراك عصي الدمع", url: "https://serv100.albumaty.com/dl/alf/om-kalsoum/Albumaty.com.Om.Kalsoum.Arak.3sy.Eldm3.MP3" },
                    { name: "كل ليلة وكل يوم", url: "https://serv100.albumaty.com/dl/alf/om-kalsoum/Albumaty.com.Om.Kalsoum.Kol.Lela.W.Kol.Youm.mp3" },
                    { name: "اصالح في روحي", url: "https://serv100.albumaty.com/dl/alf/om-kalsoum/Albumaty.com.Om.Kalsoum.Ghlbt.Asal7.Fe.Ro7y.mp3" }
                ]
            },

            // ===================== وائل كفوري =====================
            {
                category: "🎶 وائل كفوري",
                items: [
                    { name: "ليل ورعد", url: "https://dl.nghmaty.com/s1/45gq16BC.mp3" },
                    { name: "مملكتى السعيدة", url: "https://abd.albumaty.com/uploads/songs/Wael.Kfoury/02.Bokra_Rayeh_Aaljish/05.Mamlikty_Elsaeda.mp3" },
                    { name: "شو مبكيكى", url: "https://abd.albumaty.com/uploads/songs/Wael.Kfoury/15.MP3/12.Shou%20Mbakiki.mp3" },
                    { name: "حبك عذاب", url: "https://abd.albumaty.com/uploads/songs/Wael.Kfoury/15.MP3/15.Hobbak_3azab.mp3" },
                    { name: "ما وعدتك", url: "https://dl.nghmaty.com/s1/85lz789Q.mp3" },
                    { name: "كلما بتشرق", url: "https://dl.nghmaty.com/s1/43Ks20g5.mp3" },
                    { name: "ميت فيكي", url: "https://dl.nghmaty.com/s1/519i62QS.mp3" },
                    { name: "سألوني", url: "https://dl.nghmaty.com/s1/80AY35JB.mp3" },
                    { name: "مغـــرورة", url: "https://dl.nghmaty.com/s1/66Uj96SN.mp3" },
                    { name: "بحبك أنا كتير", url: "https://dl.nghmaty.com/s1/92bC22S8.mp3" },
                    { name: "هلا تا فقتي", url: "https://serv100.albumaty.com/dl/waw/wael-kafoury/albums/w.2017/09_-_Halla_Ta_Feati.mp3" },
                    { name: "بعيوني", url: "https://serv100.albumaty.com/dl/waw/wael-kafory/singels/Albumaty.Com.Wael.Kafory.Bauouny.mp3" },
                    { name: "لو حبنا", url: "https://dl.nghmaty.com/s1/87Oc517t.mp3" },
                    { name: "تك تك قلبي", url: "https://serv100.albumaty.com/2025/Albumaty.Com_wail_kfwry_tk_tk_klby.mp3" },
                    { name: "الخاشوقه", url: "https://dl.nghmaty.com/s1/64E276uK.mp3" }
                ]
            },

            // ===================== عبد الحليم =====================
            {
                category: "🎙️ عبد الحليم حافظ",
                items: [
                    { name: "اهواك", url: "https://dl.nghmaty.com/s1/77qH39gB.mp3" },
                    { name: "على قد الشوق", url: "https://dl.nghmaty.com/s1/63nR954q.mp3" },
                    { name: "اسأل علي مرة", url: "https://dl.nghmaty.com/s1/12Yr57xh.mp3" },
                    { name: "جانا الهوا", url: "https://dl.nghmaty.com/s1/13sI806q.mp3" },
                    { name: "بحبك", url: "https://ls11.arabmelody.net/2016/L7n_me_7bebe_43103.mp3" },
                    { name: "لا تلومني", url: "https://ls11.arabmelody.net/2016/L7n_me_Latlomene_43104.mp3" },
                    { name: "يتمنى", url: "https://ls11.arabmelody.net/2016/L7n_me_7d_Etmne_43119.mp3" },
                    { name: "جروح قلبي", url: "https://ls11.arabmelody.net/2016/L7n_me_Gro7_Qlbe_43121.mp3" },
                    { name: "عزيز النفس", url: "https://ls11.arabmelody.net/2016/L7n_me_Azez_Al-nfs_43157.mp3" },
                    { name: "احذرك", url: "https://ls11.arabmelody.net/2016/L7n_me_A7dhrk_43142.mp3" },
                    { name: "النهاية", url: "https://ls11.arabmelody.net/2016/L7n_me_O9lna_Llnhaeh_43111.mp3" }
                ]
            },

            // ===================== هاني شاكر =====================
            {
                category: "🎤 هاني شاكر",
                items: [
                    { name: "مشتريكي", url: "https://dl.nghmaty.com/serv1/songs/Hany%20Shaker/28.Elhob%20Malosh%20keber/8.Meshtriky.mp3" },
                    { name: "ايه يالى بحبك", url: "https://dl.nghmaty.com/serv1/songs/Hany%20Shaker/28.Elhob%20Malosh%20keber/2.Eh%20Yaly%20Ba7bk.mp3" },
                    { name: "برئ منك", url: "https://dl.nghmaty.com/serv1/songs/Hany%20Shaker/24.Wala%20Kan%20Beamry/06.Baree2%20Mennek.mp3" },
                    { name: "جرحى انا", url: "https://dl.nghmaty.com/serv1/songs/Hany%20Shaker/20.Garhy%20Ana/4.Garhy%20Ana.mp3" },
                    { name: "لسه بتسالى", url: "https://dl.nghmaty.com/serv1/songs/Hany%20Shaker/13.Bahbak%20Ana%202002/4.Lessa%20Btesa2ali.mp3" },
                    { name: "لو كان بايدى", url: "https://dl.nghmaty.com/serv1/songs/Hany%20Shaker/10.Teslamly%20Oyouno/03.Law%20Kan%20Beidy.mp3" },
                    { name: "حكاية كل عاشق", url: "https://dl.nghmaty.com/serv1/songs/Hany%20Shaker/08.Hekayet%20kol%20Ashek/3.Hekayet%20Kol%203asheq.mp3" },
                    { name: "نسيانك صعب اكيد", url: "https://dl.nghmaty.com/serv1/songs/Hany%20Shaker/07.Shawer/05.Nesyanak%20Sa3b%20Akid.mp3" },
                    { name: "ارجوكى يا حبيبتى", url: "https://dl.nghmaty.com/serv1/songs/Hany%20Shaker/29.Ma3ak/05.Argouky%20Ya%20Habibty.mp3" },
                    { name: "مش بعتب عليك", url: "https://dl.nghmaty.com/serv1/songs/Hany%20Shaker/28.Elhob%20Malosh%20keber/1.Mosh%20Ba3tab%203alek.mp3" },
                    { name: "ماتهدديش", url: "https://dl.nghmaty.com/serv1/songs/Hany%20Shaker/26.kolo%20Yehoun/02.Ma%20tehadedish.mp3" },
                    { name: "معقول", url: "https://dl.nghmaty.com/serv1/songs/Hany%20Shaker/25.Alby%20Malo/04.Ma32ool.mp3" },
                    { name: "ارجعيلى", url: "https://dl.nghmaty.com/serv1/songs/Hany%20Shaker/21.El%20Helm%20Algameil/3.Erga3ely.mp3" },
                    { name: "اسمعينى", url: "https://dl.nghmaty.com/serv1/songs/Hany%20Shaker/21.El%20Helm%20Algameil/7.Esma3einy.mp3" },
                    { name: "تخسرى", url: "https://dl.nghmaty.com/serv1/songs/Hany%20Shaker/22.Tekhsary/05.Tekhsary.mp3" },
                    { name: "كتبتلى السنين", url: "https://dl.nghmaty.com/serv1/songs/Hany%20Shaker/28.Elhob%20Malosh%20keber/6.Katabtlly%20Al%20Senen.mp3" },
                    { name: "اصاحب مين", url: "https://dl.nghmaty.com/serv1/songs/Hany%20Shaker/28.Elhob%20Malosh%20keber/3.Asa7eb%20Men.mp3" },
                    { name: "اتمدت الايدين", url: "https://dl.nghmaty.com/serv1/songs/Hany%20Shaker/07.Shawer/03.Etmadet%20El%20Edein.mp3" },
                    { name: "بكل العمر", url: "https://dl.nghmaty.com/serv1/songs/Hany%20Shaker/06.Bahebak%20Yaghaly/6.Be%20Kol%20El%20Omr.mp3" },
                    { name: "غرباء", url: "https://dl.nghmaty.com/serv1/songs/Hany%20Shaker/10.Teslamly%20Oyouno/07.Ghoraba.mp3" },
                    { name: "بتحبيه", url: "https://dl.nghmaty.com/serv1/songs/Hany%20Shaker/05.Qarbni%20Leek/05.Bethebih.mp3" }
                ]
            },

            // ===================== عمرو دياب =====================
            {
                category: "🎵 عمرو دياب",
                items: [
                    { name: "ونندم", url: "https://serv100.albumaty.com/dl/3en/amr-diab/albums/matkhafesh/03.We_Nendam.mp3" },
                    { name: "ليلي نهاري", url: "https://serv100.albumaty.com/dl/3en/amr-diab/albums/lely-nhary/01.Lealy_Nahary.mp3" },
                    { name: "بابا", url: "https://serv100.albumaty.com/2024/Albumaty.Com_amrw_dyab_baba.mp3" },
                    { name: "حبيتها", url: "https://serv100.albumaty.com/dl/3en/amr-diab/albums/matkhafesh/08.Habitha.mp3" },
                    { name: "حبيبي", url: "https://serv100.albumaty.com/dl/3en/amr-diab/albums/habeby/01.Habibi.mp3" },
                    { name: "عوّدوني", url: "https://serv100.albumaty.com/dl/3en/amr-diab/albums/awedony/01.Awedony.mp3" },
                    { name: "راجعين", url: "https://serv100.albumaty.com/dl/3en/amr-diab/albums/rag3een/01.Ragein.mp3" },
                    { name: "العو", url: "https://serv100.albumaty.com/dl/dal/diab/albums/el3aw/01.El_Aaw.mp3" }
                ]
            },

            // ===================== نجوم آخرون =====================
            {
                category: "⭐ نجوم آخرون",
                items: [
                    { name: "محمود العسيلي - واخيرا", url: "https://serv2.albumaty.com/2025/Albumaty.Com_mhmwd_alasyly_wakhyra__-_mslsl_atnyn_ghyrna_-_ma_sabryn.mp3" },
                    { name: "رامي جمال - الندم", url: "https://serv100.albumaty.com/dl/reh/ramy-gamal/albums/malnash-ella-ba3d/09_-_El_Nadam.mp3" },
                    { name: "رامي جمال - مات اللي فات", url: "https://serv100.albumaty.com/dl/reh/ramy-gamal/albums/malnash-ella-ba3d/11_-_Mat_Elly_Fat.mp3" },
                    { name: "شيرين - اللي يقابل حبيبي", url: "https://serv100.albumaty.com/2024/Albumaty.Com_shyryn_ally_ykabl_hbyby.mp3" },
                    { name: "شيرين - منوعات", url: "https://dl.nghmaty.com/serv9/songs/Nghmaty.CoM_shyryn_mnwaat_7zynt_w_rwmansyt.mp3" },
                    { name: "أصالة - يا صابرة", url: "https://serv100.albumaty.com/dl/alf/asala/albums/ya-sabra-yan/01.Ya_Sabra_Yana.mp3" },
                    { name: "أصالة - هات قلبي", url: "https://serv100.albumaty.com/dl/alf/asala/albums/ya-sabra-yan/03.Hat_Qalbi.mp3" },
                    { name: "أصالة - سامحتك كتير", url: "https://serv100.albumaty.com/dl/alf/asala/albums/ya-sabra-yan/04.Samahtak_Keteer.mp3" },
                    { name: "أصالة - لو تعرفوة", url: "https://serv100.albumaty.com/dl/alf/asala/albums/ya-sabra-yan/02.Laou_Taarefou.mp3" },
                    { name: "أصالة - توأم الروح", url: "https://serv100.albumaty.com/dl/alf/asala/albums/tw2m-alroh/02.Tawaam_Al_Rouh.mp3" },
                    { name: "أصالة - الا انت", url: "https://dl.nghmaty.com/serv1/songs/Assala/21.Wala.Tesadak/07.Ela%20Enta.mp3" },
                    { name: "أصالة - احنا بنستناك", url: "https://dl.nghmaty.com/serv1/songs/Assala/04.Aghla.Elbashar/01.Ahna%20Benstanak.mp3" },
                    { name: "أصالة - شيء بيرجعلك", url: "https://dl.nghmaty.com/serv1/songs/Assala/19.Ergaa.Laha/02.Shee%20Yarga%20Lak.mp3" },
                    { name: "أصالة - كتبتك", url: "https://dl.nghmaty.com/serv1/songs/Assala/19.Ergaa.Laha/04.Katabtak.mp3" },
                    { name: "ديانا حداد - بزعل منك", url: "https://dl.nghmaty.com/s1/71ri41hZ.mp3" },
                    { name: "ديانا حداد - ماني", url: "https://dl.nghmaty.com/s1/61ez43Eg.mp3" },
                    { name: "ديانا حداد - عوايد", url: "https://dl.nghmaty.com/s1/39he67Dg.mp3" },
                    { name: "ديانا حداد - شاطر", url: "https://dl.nghmaty.com/s1/81GW14V8.mp3" },
                    { name: "ديانا حداد - بريــــئة", url: "https://dl.nghmaty.com/s1/14sY24CQ.mp3" },
                    { name: "ديانا حداد - لقيتك", url: "https://dl.nghmaty.com/s1/74MP47wn.mp3" },
                    { name: "ديانا حداد - يا سامعين", url: "https://dl.nghmaty.com/s1/29Jn89qv.mp3" },
                    { name: "ديانا حداد - امشي", url: "https://dl.nghmaty.com/s1/880u36gI.mp3" },
                    { name: "ديانا حداد - يامايا", url: "https://dl.nghmaty.com/s1/90lA14St.mp3" },
                    { name: "فارس كرم - تقبر قلبي", url: "https://dl.nghmaty.com/s1/50At845o.mp3" },
                    { name: "فارس كرم - مملكتى السعيدة", url: "https://dl.nghmaty.com/s1/519i62QS.mp3" },
                    { name: "فارس كرم - موليتين", url: "https://dl.nghmaty.com/s1/94Kz70Lj.mp3" },
                    { name: "فارس كرم - دخيلو", url: "https://dl.nghmaty.com/s1/37GQ71Rk.mp3" },
                    { name: "فارس كرم - التنورة", url: "https://dl.nghmaty.com/s1/18Pq70q4.mp3" },
                    { name: "فارس كرم - قلة أدب", url: "https://serv100.albumaty.com/2025/Albumaty.Com_fars_krm_klt_adb.mp3" },
                    { name: "فارس كرم - ما بقعد بلاكي", url: "https://dl.nghmaty.com/s1/16yq64CG.mp3" },
                    { name: "فارس كرم - جانن", url: "https://dl.nghmaty.com/s1/650882SB.mp3" },
                    { name: "فارس كرم - الاخرس", url: "https://dl.nghmaty.com/s1/60Cy705o.mp3" },
                    { name: "فارس كرم - الله وكيلك", url: "https://dl.nghmaty.com/s1/95Wf68kV.mp3" },
                    { name: "فارس كرم - ختيار عالعكازة", url: "https://dl.nghmaty.com/s1/13YV76Az.mp3" },
                    { name: "فارس كرم - يا حنون", url: "https://dl.nghmaty.com/s1/49vr73fp.mp3" },
                    { name: "فارس كرم - شفــــتا", url: "https://dl.nghmaty.com/s1/56ok26gJ.mp3" },
                    { name: "ملحم زين - مدري شو بني", url: "https://abd.albumaty.com/files/mp3/abdwap2.Com_ml7m_zyn_mdry_shw_bny.mp3" },
                    { name: "ملحم زين - يا صغيري", url: "https://dl.nghmaty.com/s1/66oR187f.mp3" },
                    { name: "ملحم زين - غيبي يا شمس", url: "https://dl.nghmaty.com/s1/24zu41ey.mp3" },
                    { name: "ملحم زين - علواه", url: "https://dl.nghmaty.com/s1/43Sf22KQ.mp3" },
                    { name: "ملحم زين - وحياة عينيك", url: "https://dl.nghmaty.com/s1/77OS922g.mp3" },
                    { name: "رولا سعد - ان راح منك يا عين", url: "https://dl.nghmaty.com/s1/82Cl94kv.mp3" },
                    { name: "رولا سعد - ميدلي", url: "https://dl.nghmaty.com/s1/29ur43iW.mp3" },
                    { name: "رولا سعد - دلوعة", url: "https://dl.nghmaty.com/s1/83Au50KZ.mp3" },
                    { name: "رولا سعد - يا دلع", url: "https://dl.nghmaty.com/s1/49qT97Kx.mp3" },
                    { name: "رولا سعد - زي العسل", url: "https://dl.nghmaty.com/s1/66lS57DL.mp3" },
                    { name: "رولا سعد - لمجوز الله يزيده", url: "https://dl.nghmaty.com/s1/40bo72mi.mp3" },
                    { name: "رولا سعد - ايام اللولو", url: "https://dl.nghmaty.com/s1/297S741j.mp3" },
                    { name: "رولا سعد - امورتي الحلوة", url: "https://dl.nghmaty.com/s1/43K584sX.mp3" },
                    { name: "رولا سعد - زقفة زقفة يا شباب", url: "https://dl.nghmaty.com/s1/76Z264bU.mp3" },
                    { name: "رولا سعد - ميدلي 1", url: "https://dl.nghmaty.com/s1/84nf29Db.mp3" },
                    { name: "نجوى كرم - نزلت البحر", url: "https://abd.albumaty.com/uploads/songs/najwa.karam.nezelt.l.ba7r.mp3" },
                    { name: "نجوى كرم - مافي نوم", url: "https://abd.albumaty.com/uploads/songs/Najwa.Karam/Najwa%20Karam%20-%20Mafi%20Noum/Najwa%20Karam%20-%20Mafi%20Noum.mp3" },
                    { name: "نجوى كرم - ولعها", url: "https://abd.albumaty.com/uploads/songs/Najwa.Karam/Najwa%20Karam%20-%20Khallini%20Shoufak/Najwa%20Karam%20-%20Wallaa.mp3" },
                    { name: "مروان خوري - لو فيي", url: "https://abd.albumaty.com/uploads/songs/Marawan.Khory/Marawan.Khory_Law.Fiyi.mp3" },
                    { name: "مروان خوري - قلبي على قلبك", url: "https://dl.nghmaty.com/serv1/songs/Marwan.Khoury/Marwan.Khoury.El-Aad.El-Aaksi/10.%20Albak%20Ala%20Albi.mp3" },
                    { name: "كاظم الساهر - اكرهها", url: "https://abd.albumaty.com/uploads/songs/KazemElSaher/1999%20Habibaty%20Wa%20Al%20Matar/05%20Akrahquha.mp3" },
                    { name: "كاظم الساهر - ضمني على صدرك", url: "https://abd.albumaty.com/uploads/songs/KazemElSaher/1995%20Ba3d%20El%207ob/02%20%20Dhomni%20Ala%20Sadrak.mp3" },
                    { name: "كاظم الساهر - اغازلك", url: "https://abd.albumaty.com/uploads/songs/KazemElSaher/2000%20Al%20Musta7eel/01%20Aghazalek.mp3" },
                    { name: "كاظم الساهر - الا انت", url: "https://abd.albumaty.com/uploads/songs/KazemElSaher/1998%20Ana%20Wi%20Laila/01%20Illa%20Antee.mp3" },
                    { name: "جورج وسوف - كلام الناس", url: "https://serv100.albumaty.com/dl/gem/george-wassouf/albums/kalam-elnass/06_Kalam_Ennas.mp3" },
                    { name: "جورج وسوف - الحب الاولاني", url: "https://serv100.albumaty.com/dl/gem/george-wassouf/albums/kalam-elnass/05_El_Houb_El_Awalani.mp3" },
                    { name: "جورج وسوف - طبيب جراح", url: "https://serv100.albumaty.com/dl/gem/george-wassouf/albums/tabeb-garah/01.Tabeeb_Garrah.mp3" },
                    { name: "جورج وسوف - حارمنا من انسك", url: "https://serv100.albumaty.com/dl/gem/george-wassouf/albums/kalam-elnass/04_Harimna_Min_Onsak.mp3" },
                    { name: "جورج وسوف - لسة الدنيا بخير", url: "https://serv100.albumaty.com/dl/gem/george-wassouf/albums/lessa-eldonia-bkheer/06._Lessa_El_Dounia_Bkheir.mp3" },
                    { name: "جورج وسوف - العوازل", url: "https://serv100.albumaty.com/dl/gem/george-wassouf/albums/lessa-eldonia-bkheer/04._El_3awazel.mp3" },
                    { name: "جورج وسوف - الحب الكبير", url: "https://serv100.albumaty.com/dl/gem/george-wassouf/albums/lessa-eldonia-bkheer/01._El_Hob_El_Kebir.mp3" },
                    { name: "جورج وسوف - ومشينا يا حبيبي", url: "https://serv100.albumaty.com/dl/gem/george-wassouf/albums/lessa-eldonia-bkheer/03._W_Mchina_Ya_Habibi.mp3" },
                    { name: "عاصي الحلاني - احبك جداً", url: "https://dl.nghmaty.com/s1/40Ve41xf.mp3" },
                    { name: "عاصي الحلاني - امشي لحالي", url: "https://dl.nghmaty.com/s1/43ML57hx.mp3" },
                    { name: "عاصي الحلاني - مالي صبر", url: "https://dl.nghmaty.com/s1/72Om80tz.mp3" },
                    { name: "عاصي الحلاني - وانا مارق مريت", url: "https://dl.nghmaty.com/s1/22kY648I.mp3" },
                    { name: "عاصي الحلاني - مثل الكذب", url: "https://dl.nghmaty.com/s1/75au79Me.mp3" },
                    { name: "عاصي الحلاني - ويلي منهم", url: "https://dl.nghmaty.com/s1/17R068eH.mp3" },
                    { name: "عاصي الحلاني - شوق الصحارى", url: "https://dl.nghmaty.com/s1/49aP97LR.mp3" },
                    { name: "علاء زلزلي - تقبرني", url: "https://dl.nghmaty.com/s1/32H7557Y.mp3" },
                    { name: "علاء زلزلي - بتعقد بتجنن", url: "https://serv4.nghmaty.com/songs2019/Nghmaty.Com_alaa_zlzly_btakd_btgnn.mp3" },
                    { name: "راشد الماجد - مشكلني", url: "https://dl.nghmaty.com/s1/61wi45vz.mp3" },
                    { name: "يحي علاء", url: "https://serv2.albumaty.com/2025/Albumaty.Com__khtfani.mp3" },
                    { name: "محمد رمضان", url: "https://serv2.albumaty.com/2025/Albumaty.Com_mhmd_rmdan_sh_sh__-_ma_lara_tramb.mp3" },
                    { name: "محمد رمضان - تيك توك", url: "https://serv100.albumaty.com/songs_2020/Albumaty.Com_mhmd_rmdan_tyk_twk.mp3" },
                    { name: "محمد رمضان - بوم بوم", url: "https://serv100.albumaty.com/dl/mem/mohamad-ramdan/singels/Albumaty.Com.Mohamed.Ramadan.BUM.BUM.mp3" },
                    { name: "حميد الشاعري", url: "https://serv2.albumaty.com/2025/Albumaty.Com_lygy_sy_kan_ali_ayny_-_ma_hmyd_alshaary.mp3" },
                    { name: "حمزة نمرة", url: "https://serv2.albumaty.com/2025/Albumaty.Com_hmzh_nmrh_ahky.mp3" },
                    { name: "بيسان اسماعيل", url: "https://serv100.albumaty.com/2024/Albumaty.Com_bysan_asmaayl_khtyt_-_ma_foad_gnyd.mp3" },
                    { name: "بيسان اسماعيل - الشيب", url: "https://od.lk/d/NTNfNTE4NTUwMTVfcjNtTEE/Alshipp.mp3" },
                    { name: "بيسان اسماعيل - العشر كفوف", url: "https://serv100.albumaty.com/song2022/Albumaty.Com_bysan_asmaayl_alashr_kfwf.mp3" },
                    { name: "بيسان اسماعيل - اقوى من الدنيا", url: "https://serv100.albumaty.com/songs_2020/Albumaty.Com_bysan_asmaayl_akwi_mn_aldnya_-_ma_abrahym_alamyr.mp3" },
                    { name: "بيسان اسماعيل - واكع بالعشك", url: "https://serv100.albumaty.com/dl/bh/besan-esmaeil/singles/Albumaty.Com.Bessan.Ismail.Wak3.Bel.3shk.mp3" },
                    { name: "بيسان اسماعيل - يموت فراكك", url: "https://serv100.albumaty.com/dl/bh/besan-esmaeil/singles/Albumaty.Com.Bessan.Ismail.Ymout.Frakak.mp3" },
                    { name: "بيسان اسماعيل - هيهات", url: "https://serv100.albumaty.com/2024/Albumaty.Com_bysan_asmaayl_hyhat.mp3" },
                    { name: "بيسان اسماعيل - تربح الحربين", url: "https://serv2.albumaty.com/2025/Albumaty.Com_bysan_asmaayl_alhrbyn.mp3" },
                    { name: "بيسان اسماعيل - الخطية", url: "https://serv100.albumaty.com/2024/Albumaty.Com_bysan_asmaayl_khtyt_-_ma_foad_gnyd.mp3" },
                    { name: "بيسان اسماعيل - الاش", url: "https://serv2.albumaty.com/2025/Albumaty.Com_bysan_asmaayl_alash.mp3" },
                    { name: "مروان بابلو", url: "https://serv100.albumaty.com/2025/Albumaty.Com_mrwan_bablw_101_(Bonus).mp3" },
                    { name: "مروان بابلو - شفت", url: "https://serv100.albumaty.com/songs_2020/Albumaty.Com_mrwan_bablw_shyft_-_ma_wygz.mp3" },
                    { name: "مروان بابلو - ديناميت", url: "https://serv100.albumaty.com/songs_2020/Albumaty.Com_mrwan_bablw_dynamyt_-_ma_mwlwtwf.mp3" },
                    { name: "مروان بابلو - الكرمة", url: "https://serv100.albumaty.com/2024/Albumaty.Com_mrwan_bablw_karma.mp3" },
                    { name: "مروان بابلو - الهلال", url: "https://serv100.albumaty.com/songs_2020/Albumaty.Com_mrwan_bablw_alhlal.mp3" },
                    { name: "مروان بابلو - الجزم", url: "https://serv100.albumaty.com/songs_2020/Albumaty.Com_mrwan_bablw_algmyzt.mp3" },
                    { name: "محمد فوزي - تعب الهوى قلبي", url: "https://dl.nghmaty.com/s1/61cf81mj.mp3" },
                    { name: "محمد عبدالوهاب - ياوابور", url: "https://dl.nghmaty.com/s1/29KE40mF.mp3" },
                    { name: "محمد عبدالوهاب - انا والعذاب", url: "https://dl.nghmaty.com/s1/42EW75Np.mp3" },
                    { name: "محمد عبدالوهاب - اجري اجري", url: "https://dl.nghmaty.com/s1/60jL92Sw.mp3" },
                    { name: "محمد عبدالوهاب - خايف اقول", url: "https://dl.nghmaty.com/s1/21tp42rN.mp3" },
                    { name: "محمد عبدالمطلب", url: "https://dl.nghmaty.com/s2/391687hg.mp3" },
                    { name: "ليلى مراد - اسال عليه", url: "https://dl.nghmaty.com/s1/21QR19Eb.mp3" },
                    { name: "ليلى مراد - انا قلبي دليلي", url: "https://dl.nghmaty.com/s1/61hL99b4.mp3" },
                    { name: "ليلى مراد - سنتين", url: "https://dl.nghmaty.com/s1/95qA99TW.mp3" },
                    { name: "ليلى مراد - حقك عليا", url: "https://dl.nghmaty.com/s2/85vZ41Qu.mp3" },
                    { name: "نجاة الصغيرة - يا مسافر وحدك", url: "https://serv100.albumaty.com/dl/non/nagat-elsaghyra/albums/ahla-nagat/Ya.Msafer.Wa7dak.mp3" },
                    { name: "نجاة الصغيرة - عاليادي", url: "https://serv100.albumaty.com/dl/non/nagat-elsaghyra/albums/ahla-nagat/3lYady.mp3" },
                    { name: "نجاة الصغيرة - القريب منك بعيد", url: "https://serv100.albumaty.com/dl/non/nagat-elsaghyra/albums/ahla-nagat/El.2reb.Mnk.B3eed.mp3" },
                    { name: "نجاة الصغيرة - اما براوة", url: "https://serv100.albumaty.com/dl/non/nagat-elsaghyra/albums/ahla-nagat/Ama.Brawa.mp3" },
                    { name: "نجاة الصغيرة - ايظن", url: "https://serv100.albumaty.com/dl/non/nagat-elsaghyra/albums/ahla-nagat/Ayzno.mp3" },
                    { name: "نجاة الصغيرة - الطير المسافر", url: "https://serv100.albumaty.com/dl/non/nagat-elsaghyra/albums/ahla-nagat/El.Ter.El.Msafer.mp3" },
                    { name: "نجاة الصغيرة - انا بعشق البحر", url: "https://serv100.albumaty.com/dl/non/nagat-elsaghyra/albums/ahla-nagat/Ana.B3sh2.El.Bhr.mp3" },
                    { name: "شادية - حاجه غريبه", url: "https://dl.nghmaty.com/s1/100O99kw.mp3" },
                    { name: "شادية - سيد الحبايب", url: "https://dl.nghmaty.com/s2/37i556zE.mp3" },
                    { name: "صباح - يانا يانا", url: "https://serv100.albumaty.com/dl/sen/sabah/29_-_Yana_Yana.mp3" },
                    { name: "صباح - يا دلع دلع", url: "https://serv100.albumaty.com/dl/sen/sabah/19_-_Ya_Dal3_Dal3.mp3" },
                    { name: "سميرة سعيد - ع البال", url: "https://serv100.albumaty.com/dl/sen/samira-said/albums/3al-bal/01.Aal_bal.mp3" },
                    { name: "سميرة سعيد - يوم ورا يوم", url: "https://serv100.albumaty.com/dl/sen/samira-said/albums/youm-wra-youm/01.Youm_Wara_Youm.mp3" },
                    { name: "أمل حجازي - زمان", url: "https://serv100.albumaty.com/dl/alf/amal-hegazy/albums/zaman/Albumaty.Com.Amal.Hegazy.Zaman.mp3" },
                    { name: "أمل حجازي - جنون بحبك", url: "https://serv100.albumaty.com/dl/alf/amal-hegazy/albums/bya3-el-ward/Albumaty.Com.Amal.Hegazy.Jnoun_Bhebak.mp3" },
                    { name: "أمل ماهر", url: "https://dl.nghmaty.com/serv9/songs/Nghmaty.CoM_amal_mahr_kwktyl_7zyn_rwmansy.mp3" },
                    { name: "شيماء الشايب - الف ليلة", url: "https://dl.nghmaty.com/serv1/songs/Shimaa.Elshayeb/Kalthomyat/05.Alf%20Laila.mp3" },
                    { name: "شيماء الشايب - بعيد عنك", url: "https://dl.nghmaty.com/serv1/songs/Shimaa.Elshayeb/Kalthomyat/02.Baeed%20Annak.mp3" },
                    { name: "شيماء الشايب - سيرة الحب", url: "https://dl.nghmaty.com/serv1/songs/Shimaa.Elshayeb/Kalthomyat/07.Seret%20El%20Hob.mp3" },
                    { name: "شيماء الشايب - انت عمرى", url: "https://dl.nghmaty.com/serv1/songs/Shimaa.Elshayeb/Kalthomyat/01.Enta%20Omry.mp3" },
                    { name: "شيماء الراسي", url: "https://od.lk/s/NTNfNTA4NTcyNzdf/nsara.mp3" },
                    { name: "شيماء الراسي - شيماء", url: "https://od.lk/s/NTNfNTA4NTYyMDhf/shimaa.mp3" },
                    { name: "شيماء الراسي - انتيني", url: "https://od.lk/d/NTNfNTA4NTcxMTFf/antini.mp3" },
                    { name: "شيماء الراسي - وادي النصارة", url: "https://od.lk/d/NTNfNTA4NTc0MzVf/WadiAlnsara.mp3" },
                    { name: "ماجدة الرومي - خدني حبيبي", url: "https://serv100.albumaty.com/dl/mem/majda-elroumi/albums/a7la-ma-ghanet/5odni_Habibi.mp3" },
                    { name: "ذكرى - فرصه اخيرة", url: "https://serv100.albumaty.com/dl/zal/zekra/albums/azab-elhawa/03_-_Forsa_A5era.mp3" },
                    { name: "ذكرى - الاسامى", url: "https://serv100.albumaty.com/dl/zal/zekra/albums/elasamy/03.El_Asamy.mp3" },
                    { name: "لطيفة - انشالله", url: "https://serv100.albumaty.com/dl/lam/latifa/albums/wade7/02_Inchallah_version_fran.mp3" },
                    { name: "لطيفة - ماتروحش بعيد", url: "https://serv100.albumaty.com/dl/lam/latifa/albums/matro7sh-b3ed/01_-_Ma_Trohsh_B3ed.mp3" },
                    { name: "أمينة - اركب الحنطور", url: "https://serv100.albumaty.com/dl/alf/amina/albums/3la-meen/03.El_Hantor.mp3" },
                    { name: "أمينة - أخيراً اتجوزت", url: "https://serv100.albumaty.com/dl/alf/amina/albums/3la-meen/02.A5eran_Etgawezt.mp3" },
                    { name: "حكيم - البعد لا", url: "https://serv100.albumaty.com/dl/7h/hakim/albums/nazra/07.El_Boad_Laa.mp3" },
                    { name: "حكيم - نار", url: "https://serv100.albumaty.com/dl/7h/hakim/albums/nar/01.Nar.mp3" },
                    { name: "روبي - ليه بيداري كده", url: "https://serv100.albumaty.com/dl/reh/ruby/albums/eb2a-2ablny/Lieh_Bydary_Keda.mp3" },
                    { name: "روبي - كل ماقوله اه", url: "https://serv100.albumaty.com/dl/reh/ruby/albums/eb2a-2ablny/Kol_Ma2olo_Ah.mp3" },
                    { name: "هشام عباس - اه من عينيها", url: "https://serv100.albumaty.com/dl/heh/hisham-abbas/albums/t3aly/03.Ah_Min_3neha.mp3" }
                ]
            }
        ];

        // ============================================================
        // الكود التالي لا يحتاج إلى تعديل
        // ============================================================
        const audioPlayer = document.getElementById('audioPlayer');
        const nowPlaying = document.getElementById('nowPlaying');
        const categoriesContainer = document.getElementById('categoriesContainer');
        
        let currentPlaylist = [];
        let currentIndex = -1;

        // ✅ الانتقال التلقائي للأغنية التالية من نفس الفئة
        audioPlayer.addEventListener('ended', function() {
            if (currentPlaylist.length > 0) {
                nextTrack();
            }
        });

        // بناء الأزرار
        function buildUI() {
            categoriesContainer.innerHTML = '';
            
            allData.forEach((cat, catIndex) => {
                const catDiv = document.createElement('div');
                catDiv.className = 'category';
                
                const title = document.createElement('div');
                title.className = 'category-title';
                title.textContent = cat.category;
                catDiv.appendChild(title);
                
                const grid = document.createElement('div');
                grid.className = 'buttons-grid';
                
                cat.items.forEach((item, itemIndex) => {
                    const btn = document.createElement('button');
                    btn.className = 'station-btn';
                    btn.textContent = item.name;
                    btn.onclick = () => playItem(catIndex, itemIndex);
                    grid.appendChild(btn);
                });
                
                catDiv.appendChild(grid);
                categoriesContainer.appendChild(catDiv);
            });
        }

        // تشغيل عنصر
        function playItem(catIndex, itemIndex) {
            const cat = allData[catIndex];
            const item = cat.items[itemIndex];
            
            // ✅ تعيين قائمة التشغيل لتكون أغاني نفس الفئة فقط
            currentPlaylist = cat.items;
            currentIndex = itemIndex;
            
            audioPlayer.src = item.url;
            audioPlayer.play().catch(e => {
                nowPlaying.textContent = '⚠️ تعذر تشغيل: ' + item.name;
            });
            nowPlaying.textContent = '▶ ' + item.name + ' - ' + cat.category;
            
            highlightActive(catIndex, itemIndex);
        }

        // تمييز الزر النشط
        function highlightActive(catIndex, itemIndex) {
            const allButtons = document.querySelectorAll('.station-btn');
            let counter = 0;
            allData.forEach((cat, ci) => {
                cat.items.forEach((item, ii) => {
                    if (ci === catIndex && ii === itemIndex) {
                        allButtons[counter].classList.add('active');
                    } else {
                        allButtons[counter].classList.remove('active');
                    }
                    counter++;
                });
            });
        }

        // التحكم
        function playPause() {
            if (audioPlayer.paused) audioPlayer.play();
            else audioPlayer.pause();
        }

        function stopTrack() {
            audioPlayer.pause();
            audioPlayer.currentTime = 0;
            nowPlaying.textContent = '⏸ تم الإيقاف';
        }

        function nextTrack() {
            if (currentPlaylist.length === 0) return;
            currentIndex = (currentIndex + 1) % currentPlaylist.length;
            playItemByIndex(currentIndex);
        }

        function prevTrack() {
            if (currentPlaylist.length === 0) return;
            currentIndex = (currentIndex - 1 + currentPlaylist.length) % currentPlaylist.length;
            playItemByIndex(currentIndex);
        }

        function playItemByIndex(index) {
            // البحث عن الفئة الحالية بناءً على قائمة التشغيل الحالية
            for (let ci = 0; ci < allData.length; ci++) {
                const cat = allData[ci];
                if (cat.items === currentPlaylist) {
                    playItem(ci, index);
                    return;
                }
            }
        }

        // بناء الواجهة عند التحميل
        buildUI();
    </script>
</body>
</html>
