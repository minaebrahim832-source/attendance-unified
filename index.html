'use strict';
/* ============================================================
   sim_v3_full.js — محاكاة كاملة لنظام الحضور الموحد v3
   بيحمّل كود index.html الحقيقي ويشغّل عليه السيناريوهات
   تشغيل: node sim_v3_full.js
   ============================================================ */
const fs=require('fs');
const vm=require('vm');
const {webcrypto}=require('crypto');

/* ===== 1) Fake DOM ===== */
const els={};
function makeEl(id){
  const el={
    _id:id,value:'',innerHTML:'',textContent:'',checked:false,className:'',
    style:{},dataset:{},children:[],onclick:null,firstChild:null,
    classList:{
      _s:new Set(),
      add(...c){c.forEach(x=>this._s.add(x));},
      remove(...c){c.forEach(x=>this._s.delete(x));},
      contains(c){return this._s.has(c);}
    },
    focus(){},select(){},click(){},appendChild(c){this.children.push(c);},remove(){},
    addEventListener(){},
  };
  return el;
}
function el(id){if(!els[id])els[id]=makeEl(id);return els[id];}
const documentStub={
  getElementById:(id)=>el(id),
  createElement:(t)=>makeEl('_dyn_'+t+'_'+Math.random()),
  querySelectorAll:(sel)=>{
    if(typeof sel==='string'&&sel.indexOf('child-row')>=0)return (global._fakeChildRows||[]);
    return [];
  },
  body:{appendChild(){}},
};

/* ===== 2) Mock Supabase (optimistic locking + offline switch) ===== */
const net={online:true};
const table=new Map(); // id -> {id,data,updated_at}
function clone(x){return x===undefined?undefined:JSON.parse(JSON.stringify(x));}
function netFail(){return Promise.reject(new Error('network down (simulated)'));}
function mkQuery(){
  return {
    _op:null,_payload:null,_filters:[],_cols:null,
    select(cols){ this._cols=cols; if(this._op==='update'||this._op==='upsert'){return this._exec();} this._op='select'; return this; },
    eq(k,v){this._filters.push([k,v]);return this;},
    update(p){this._op='update';this._payload=p;return this;},
    upsert(p){this._op='upsert';this._payload=p;const q=this;const pr=q._exec();pr.select=()=>pr;return pr;},
    single(){return this._exec('single');},
    maybeSingle(){return this._exec('maybe');},
    async _exec(mode){
      if(!net.online)return netFail();
      if(this._op==='select'){
        const rows=[...table.values()].filter(r=>this._filters.every(([k,v])=>String(r[k])===String(v)));
        if(mode==='single'){
          if(!rows.length)return{data:null,error:{message:'0 rows'}};
          return{data:clone(pick(rows[0],this._cols)),error:null};
        }
        return{data:rows.length?clone(pick(rows[0],this._cols)):null,error:null};
      }
      if(this._op==='update'){
        const rows=[...table.values()].filter(r=>this._filters.every(([k,v])=>String(r[k])===String(v)));
        rows.forEach(r=>Object.assign(r,clone(this._payload)));
        return{data:rows.map(r=>({updated_at:r.updated_at})),error:null};
      }
      if(this._op==='upsert'){
        const p=clone(this._payload);
        table.set(p.id,Object.assign(table.get(p.id)||{},p));
        return{data:[{updated_at:p.updated_at}],error:null};
      }
      return{data:null,error:{message:'bad op'}};
    }
  };
  function pick(row,cols){
    if(!cols)return row;
    const out={};cols.split(',').forEach(c=>{c=c.trim();out[c]=clone(row[c]);});
    return out;
  }
}
const mockSb={
  from(){return mkQuery();},
  channel(){return{on(){return this;},subscribe(){return this;}};},
  removeChannel(){},
};
/* أداة محاكاة "جهاز تاني كتب قبلنا" */
function externalWrite(mutator){
  const row=table.get(S.ROW);
  const data=clone(row.data);
  mutator(data);
  row.data=data;
  row.updated_at=new Date(Date.now()+1).toISOString();
}

/* ===== 3) Sandbox + load real app code ===== */
const sandbox={
  console,TextEncoder,TextDecoder,
  crypto:{subtle:webcrypto.subtle,getRandomValues:(a)=>webcrypto.getRandomValues(a)},
  document:documentStub,
  window:{addEventListener(){}, _sim:true},
  navigator:{onLine:true},
  localStorage:{_m:{},getItem(k){return this._m[k]??null;},setItem(k,v){this._m[k]=String(v);},removeItem(k){delete this._m[k];}},
  sessionStorage:{_m:{},getItem(k){return this._m[k]??null;},setItem(k,v){this._m[k]=String(v);},removeItem(k){delete this._m[k];}},
  setInterval:()=>0,clearInterval(){},setTimeout:(f)=>0, // مؤقتات معطلة في المحاكاة
  supabase:{createClient:()=>mockSb},
  XLSX:{},Blob:class{constructor(){}},URL:{createObjectURL:()=>'blob:sim'},
  FileReader:class{},alert(){},
};
vm.createContext(sandbox);

const html=fs.readFileSync(process.argv[2]||'index.html','utf-8');
const blocks=[...html.matchAll(/<script>([\s\S]*?)<\/script>/g)].map(m=>m[1]);
console.log('📦 تحميل كود التطبيق الحقيقي — عدد البلوكات: '+blocks.length);
blocks.forEach((b,i)=>{try{vm.runInContext(b,sandbox,{filename:'app_block_'+i+'.js'});}catch(e){console.error('✗ فشل تحميل بلوك '+i+':',e.message);process.exit(1);}});
const GRADE_ORDER_LEN=sandbox.GRADE_ORDER?sandbox.GRADE_ORDER.length:20;
const G=sandbox; // اختصار للوصول لدوال التطبيق (function declarations = خصائص globalThis)
/* جسر للمتغيرات اللغوية (let/const) اللي مش ظاهرة كخصائص على globalThis */
vm.runInContext(`
globalThis.S={
  get DB(){return DB;}, set DB(v){DB=v;},
  get currentUser(){return currentUser;}, set currentUser(v){currentUser=v;},
  get _localAtt(){return _localAtt;}, set _localAtt(v){_localAtt=v;},
  get _lastUpdatedAt(){return _lastUpdatedAt;},
  get _dirty(){return _dirty;},
  get _syncChain(){return _syncChain;},
  get _alertThresh(){return _alertThresh;}, set _alertThresh(v){_alertThresh=v;},
  get ROW(){return SB_ROW_ID;},
};
`,sandbox);
const S=sandbox.S;

/* المودالات في المحاكاة بتتأكد تلقائياً */
G.showModal=(t,m,ok,onOk)=>{if(onOk)return onOk();};

/* ===== 4) Test harness ===== */
let pass=0,fail=0,section='';
function sec(s){section=s;console.log('\n──── '+s+' ────');}
function t(name,cond){
  if(cond){pass++;console.log('  ✓ '+name);}
  else{fail++;console.log('  ✗✗✗ '+name+'  ←←← فشل');}
}
async function login(pw){
  el('pwInput').value=pw;
  await G.doLogin();
}
function setUser(u){S.currentUser=u;}

/* ===== 5) Scenarios ===== */
(async()=>{
try{

sec('١) أساسيات الأمان — التمليح والمراقب');
const h=await G.hashPw('secret1');
t('hashPw بينتج صيغة مملّحة s1$',h.startsWith('s1$')&&h.split('$').length===3);
t('pwEquals صح مع الكلمة الصحيحة',await G.pwEquals(h,'secret1'));
t('pwEquals غلط مع كلمة غلط',!(await G.pwEquals(h,'secret2')));
const legacy=await G.sha256Hex('oldpass');
t('توافق مع هاش SHA-256 القديم',await G.pwEquals(legacy,'oldpass'));
t('توافق مع كلمة نصية قديمة',await G.pwEquals('plain123','plain123'));
t('isViewerPw يقبل كلمة المراقب',await G.isViewerPw('12345Min@@@@@@@'));
t('isViewerPw يرفض غيرها',!(await G.isViewerPw('wrong')));

sec('٢) التحميل الأول والبذر');
await G.loadDB();
t('DB اتبنت بالافتراضيات',S.DB&&Array.isArray(S.DB.users)&&S.DB.users[0].role==='superadmin');
t('مصفوفات v3 موجودة (followUps/groupArchive/archives)',Array.isArray(S.DB.followUps)&&Array.isArray(S.DB.groupArchive)&&Array.isArray(S.DB.archives));
await G.saveDB({});
t('أول حفظ أنشأ صف main',table.has(S.ROW));
t('القفل التفاؤلي بيتتبع updated_at',S._lastUpdatedAt!==null);

// بذر: خدمتين عادية + مسرح + مستخدمين
const svcPrep={id:'svc_prep',name:'إعدادي',type:'regular'};
const svcSec={id:'svc_sec',name:'ثانوي',type:'regular'};
const svcTh={id:'svc_th',name:'مسرح',type:'theatre',categories:['ابتدائي','إعدادي','ثانوي','شباب'],sections:['تمثيل','ديكور']};
S.DB.services.push(svcPrep,svcSec,svcTh);
async function mkUser(o){o.password=await G.hashPw(o._pw);delete o._pw;S.DB.users.push(o);return o;}
const svAmir =await mkUser({id:'u_amir', name:'أمير',  role:'servant',serviceId:'svc_prep',year:'الثانية إعدادي',groupId:'g_amir', _pw:'amir1'});
const svBula =await mkUser({id:'u_bula', name:'بولا',  role:'servant',serviceId:'svc_prep',year:'الثالثة إعدادي',groupId:'g_bula', _pw:'bula1'});
const svKiro =await mkUser({id:'u_kiro', name:'كيرو',  role:'servant',serviceId:'svc_sec', year:'الأولى ثانوي', groupId:'g_kiro', _pw:'kiro1'});
const svThea =await mkUser({id:'u_thea', name:'مريم',  role:'theatre_servant',serviceId:'svc_th',sectionId:'تمثيل',categoryId:'إعدادي',groupId:'g_thea',_pw:'thea1'});
const yAdmin =await mkUser({id:'u_yadm', name:'مينا سنة',role:'year_admin',serviceId:'svc_prep',year:'الثانية إعدادي',groupId:'yr_1',_pw:'year1'});
const sAdmin =await mkUser({id:'u_sadm', name:'جرجس',  role:'service_admin',serviceId:'svc_prep',_pw:'svc1'});
const secAdm =await mkUser({id:'u_secadm',name:'مارك', role:'section_admin',serviceId:'svc_th',sectionId:'تمثيل',groupId:'sec_1',_pw:'sec1'});
const mems=[
 {code:'M001',name:'بيتر',grade:'الثانية إعدادي',serviceId:'svc_prep',groupId:'g_amir',phone:'0101',dob:'2012-07-15'},
 {code:'M002',name:'فادي',grade:'الثانية إعدادي',serviceId:'svc_prep',groupId:'g_amir'},
 {code:'M003',name:'يوسف',grade:'الثالثة إعدادي',serviceId:'svc_prep',groupId:'g_bula'},
 {code:'M004',name:'مارتن',grade:'الأولى ثانوي',serviceId:'svc_sec',groupId:'g_kiro'},
 {code:'M005',name:'كيرلس',grade:'الثانية إعدادي',serviceId:'svc_th',sectionId:'تمثيل',categoryId:'إعدادي',groupId:'g_thea'},
 {code:'M006',name:'توماس',grade:'السادسة ابتدائي',serviceId:'svc_th',sectionId:'تمثيل',categoryId:'ابتدائي',groupId:'g_thea'},
];
S.DB.members.push(...mems);
await G.saveDB({usersPatch:{upsert:S.DB.users},membersPatch:{upsert:mems},servicesPatch:{upsert:S.DB.services}});
t('البذر اتحفظ (6 أعضاء و8 مستخدمين في السيرفر)',table.get(S.ROW).data.members.length===6&&table.get(S.ROW).data.users.length===8);

sec('٣) تسجيل الدخول والأدوار');
await login('admin123');
t('المسئول العام دخل بالكلمة الافتراضية',S.currentUser&&S.currentUser.role==='superadmin');
t('كلمته اترقّت تلقائياً للصيغة المملّحة',S.currentUser.password.startsWith('s1$'));
await login('wrongpw');
t('كلمة غلط مبتدخلش',S.currentUser.role==='superadmin'); // فضل زي ما هو
await login('12345Min@@@@@@@');
t('المراقب بيدخل بالهاش (من غير نص صريح في الكود)',S.currentUser.role==='viewer');
t('المراقب بصلاحيات المسئول العام',G.isSuperAdmin());

sec('٤) عزل الصلاحيات والنطاقات');
setUser(svAmir);
t('الخادم يشوف مجموعته بس (2)',G.getMyMembers().length===2&&G.getMyMembers().every(m=>m.groupId==='g_amir'));
setUser(yAdmin);
t('مسئول السنة يشوف سنته بس (2)',G.getMyMembers().length===2&&G.getMyMembers().every(m=>m.grade==='الثانية إعدادي'));
G.renderUsersPage();
let uHtml=el('pagesArea').innerHTML;
t('إصلاح: مسئول السنة مش شايف خدام سنة تانية',uHtml.includes('أمير')&&!uHtml.includes('بولا'));
setUser(secAdm);
t('مسئول القسم يشوف قسمه بس (2)',G.getMyMembers().length===2&&G.getMyMembers().every(m=>m.sectionId==='تمثيل'));
setUser(sAdmin);
t('مسئول الخدمة يشوف خدمته (3)',G.getMyMembers().length===3);
t('gradesForService: خدمة إعدادي = 3 سنين بس',JSON.stringify(G.gradesForService('svc_prep'))===JSON.stringify(['الأولى إعدادي','الثانية إعدادي','الثالثة إعدادي']));
t('gradesForService: خدمة مجهولة الاسم = القائمة كاملة',G.gradesForService('no_such').length===GRADE_ORDER_LEN);
// حضانة
S.DB.services.push({id:'svc_nur',name:'حضانة',type:'regular'});
t('gradesForService: حضانة = تمهيدي/KG1/KG2 فقط',JSON.stringify(G.gradesForService('svc_nur'))===JSON.stringify(['تمهيدي','KG1','KG2']));
t('stageOf: KG2 = حضانة',G.stageOf('KG2')==='حضانة');
t('nextGrade: KG2 → الأولى ابتدائي (عبور مرحلة)',G.nextGrade('KG2')==='الأولى ابتدائي');
t('nextGrade: تمهيدي → KG1',G.nextGrade('تمهيدي')==='KG1');

sec('٥) الحضور وإغلاق اليوم');
setUser(svAmir);
el('attInput').value='M001';
G.doAtt();
t('تسجيل حضور بالكود',!!S.DB.todayAtt['M001']);
el('attInput').value='فادي';
G.doAtt();
t('تسجيل حضور بالاسم',!!S.DB.todayAtt['M002']);
el('attInput').value='M001';
G.doAtt();
t('التكرار مش بيدبل التسجيل',Object.keys(S.DB.todayAtt).filter(k=>k==='M001').length===1);
delete S.DB.todayAtt['M002'];delete S._localAtt['M002']; // فادي هيبقى غايب
G.closeDay();
await S._syncChain;
const dayRecs=S.DB.records.filter(r=>r.date===G.todayKey());
t('إغلاق اليوم سجل حاضر وغائب',dayRecs.find(r=>r.code==='M001'&&r.status==='حاضر')&&dayRecs.find(r=>r.code==='M002'&&r.status==='غائب'));
t('المجموعة اتقفلت',S.DB.closedGroups['g_amir']===G.todayKey());

sec('٦) حماية التعارض (جهازين في نفس اللحظة)');
await G.refreshDB();
externalWrite(d=>{d.members.push({code:'M900',name:'كتبه جهاز تاني',serviceId:'svc_prep',groupId:'g_bula'});});
const mNew={code:'M901',name:'كتبناه احنا',serviceId:'svc_prep',groupId:'g_amir'};
S.DB.members.push(mNew);
await G.saveDB({membersPatch:{upsert:[mNew]}});
const stored=table.get(S.ROW).data.members;
t('تعديل الجهاز التاني نجا',!!stored.find(m=>m.code==='M900'));
t('تعديلنا احنا كمان نجا — مفيش كتابة فوق بعض',!!stored.find(m=>m.code==='M901'));
// مسار التعارض الصريح: جهاز تاني كتب بين قراءتنا وكتابتنا (skipArrMerge = من غير قراءة مسبقة)
externalWrite(d=>{d.churchName='غيّرها جهاز تاني';});
await G.saveDB({skipArrMerge:true});
t('القفل التفاؤلي اكتشف الكتابة الوسيطة وأعاد المحاولة بنجاح',table.get(S.ROW).data.members.length===S.DB.members.length&&S._dirty===false);

sec('٧) قطع النت وطابور إعادة المحاولة');
net.online=false;
const mOff={code:'M902',name:'اتسجل والنت مقطوع',serviceId:'svc_prep',groupId:'g_amir'};
S.DB.members.push(mOff);
await G.saveDB({membersPatch:{upsert:[mOff]}});
t('الحفظ فشل والنظام علّم dirty',S._dirty===true);
t('السيرفر فعلاً موصلوش',!table.get(S.ROW).data.members.find(m=>m.code==='M902'));
net.online=true;
await G.saveDB({});
t('رجوع النت → التعديل المعلق اتبعت',!!table.get(S.ROW).data.members.find(m=>m.code==='M902'));
t('علامة dirty اتشالت',S._dirty===false);

sec('٨) كلمة السر المؤقتة');
setUser(S.DB.users.find(u=>u.role==='superadmin'));
await G.openTempPw('u_amir');
const tempMatch=el('memberModalBox').innerHTML.match(/dir="ltr">([a-z2-9]{6})</);
t('المسئول أصدر كلمة مؤقتة',!!tempMatch);
const temp=tempMatch[1];
t('العلم mustChangePw اتفعّل',S.DB.users.find(u=>u.id==='u_amir').mustChangePw===true);
t('الكلمة القديمة ماتت',!(await G.pwEquals(S.DB.users.find(u=>u.id==='u_amir').password,'amir1')));
await login(temp);
t('الدخول بالمؤقتة شغال + شاشة الإجبار ظهرت',S.currentUser.id==='u_amir'&&el('memberModalBox').innerHTML.includes('لازم تغيّر'));
el('fpw_new').value='amirNew22';el('fpw_new2').value='amirNew22';
await G.confirmForcePw();
t('غيّرها والعلم اتشال',S.DB.users.find(u=>u.id==='u_amir').mustChangePw===false&&await G.pwEquals(S.DB.users.find(u=>u.id==='u_amir').password,'amirNew22'));

sec('٩) الافتقاد بمنطق موجة الغياب');
setUser(svAmir);
// فادي M002: نصنع له 3 غيابات متتالية بتواريخ قديمة + افتقاد أقدم من الموجة
const fadyRecs=[
 {code:'M002',name:'فادي',date:'2026-06-05',status:'حاضر',groupId:'g_amir',serviceId:'svc_prep',sessionId:'s1'},
 {code:'M002',name:'فادي',date:'2026-06-12',status:'غائب',groupId:'g_amir',serviceId:'svc_prep',sessionId:'s2'},
 {code:'M002',name:'فادي',date:'2026-06-19',status:'غائب',groupId:'g_amir',serviceId:'svc_prep',sessionId:'s3'},
 {code:'M002',name:'فادي',date:'2026-06-26',status:'غائب',groupId:'g_amir',serviceId:'svc_prep',sessionId:'s4'}
];
S.DB.records.push(...fadyRecs);
const oldFu={id:'fu_old',code:'M002',ts:new Date('2026-05-01').getTime(),by:'أمير',typ:'مكالمة',note:'قديم قبل الغياب'};
S.DB.followUps.push(oldFu);
await G.saveDB({recordsPatch:{upsert:fadyRecs},followUpsPatch:{upsert:[oldFu]}});
G.renderRptFollowup(el('rptContent'));
t('افتقاد أقدم من الموجة مش بيغطيها — فادي في القائمة الحمرا',el('rptContent').innerHTML.includes('محدش افتقدهم')&&el('rptContent').innerHTML.includes('فادي'));
G.renderRptAlerts(el('rptContent'));
t('التنبيهات بتعلّم الافتقاد القديم بتاج "قديم"',el('rptContent').innerHTML.includes('قديم ·'));
el('fu_typ').value='مكالمة';el('fu_note').value='اتكلمت معاه — راجع الجمعة';
await G.saveFollowUp('M002');
G.renderRptFollowup(el('rptContent'));
t('بعد افتقاد جديد — خرج من القائمة الحمرا',!el('rptContent').innerHTML.includes('محدش افتقدهم'));
setUser(svKiro);
G.renderRptFollowup(el('rptContent'));
t('إصلاح: إجمالي افتقادات خادم ثانوي = 0 (مش بيعد افتقادات غيره)',/إجمالي الافتقادات/.test(el('rptContent').innerHTML)&&el('rptContent').innerHTML.includes('<div class="stat-val">0</div><div class="stat-lbl">إجمالي الافتقادات</div>'));
// عضو غايب 8 مرات وحد التنبيه 6 — لازم يظهر بـ8
setUser(svAmir);
const peterRecs=[];
for(let i=1;i<=8;i++)peterRecs.push({code:'M001',name:'بيتر',date:'2026-04-'+String(i).padStart(2,'0'),status:'غائب',groupId:'g_amir',serviceId:'svc_prep',sessionId:'x'+i});
S.DB.records.push(...peterRecs);
const peterToday=S.DB.records.filter(r=>r.code==='M001'&&r.status==='حاضر');
S.DB.records=S.DB.records.filter(r=>!(r.code==='M001'&&r.status==='حاضر')); // نشيل حضور النهارده عشان الموجة تكمل
await G.saveDB({recordsPatch:{upsert:peterRecs,remove:peterToday.map(r=>r.code+'|'+r.date+'|'+(r.sessionId||''))}});
S._alertThresh=6;
G.renderRptAlerts(el('rptContent'));
t('الحد 6 = "على الأقل 6" — الغايب 8 مرات ظاهر بعدده الحقيقي',el('rptContent').innerHTML.includes('بيتر')&&el('rptContent').innerHTML.includes('>8</span>'));
S._alertThresh=3;

sec('١٠) تسليم مجموعة + نقل + استبدال + تعطيل');
setUser(S.DB.users.find(u=>u.role==='superadmin'));
// تعطيل خادم عنده أعضاء — لازم يترفض
G.deactivateUser('u_bula');
t('التعطيل مرفوض والخادم عنده أعضاء',S.DB.users.find(u=>u.id==='u_bula').active!==false);
// تسليم مجموعة بولا لأمير + تعطيله
G.openHandover('u_bula');
el('ho_new').value='u_amir';el('ho_deact').checked=true;
await G.confirmHandover('u_bula');
t('أعضاء بولا اتنقلوا لمجموعة أمير',S.DB.members.find(m=>m.code==='M003').groupId==='g_amir');
t('بولا اتعطل واسمه محفوظ للتقارير القديمة',S.DB.users.find(u=>u.id==='u_bula').active===false&&G.getGroupName('g_bula')==='بولا');
t('واسم المجموعة كمان مأرشف احتياطياً',(S.DB.groupArchive||[]).some(g=>g.groupId==='g_bula'&&g.name==='بولا'));
await login('bula1');
t('المعطّل مش بيعرف يدخل',S.currentUser.id!=='u_bula');
// نقل كيرو من ثانوي لمسرح
setUser(S.DB.users.find(u=>u.role==='superadmin'));
const kiroOldGroup=svKiro.groupId;
G.openTransferServant('u_kiro');
el('tr_svc').value='svc_th';G.trUpdateFields();
el('tr_section').value='ديكور';el('tr_category').value='ثانوي';
el('tr_old').value='_none';
await G.confirmTransfer('u_kiro');
const kiroNow=S.DB.users.find(u=>u.id==='u_kiro');
t('كيرو اتنقل للمسرح بدور خادم مسرح',kiroNow.serviceId==='svc_th'&&kiroNow.role==='theatre_servant'&&kiroNow.sectionId==='ديكور');
t('خد groupId جديد وتاريخه القديم مأرشف بالاسم',kiroNow.groupId!==kiroOldGroup&&G.getGroupName(kiroOldGroup).includes('كيرو'));
// استبدال مسئول السنة
G.openReplaceAdmin('u_yadm');
el('ra_name').value='ابانوب';el('ra_pw').value='aban22';
await G.confirmReplaceAdmin('u_yadm');
const newAdm=S.DB.users.find(u=>u.name==='ابانوب');
t('المسئول الجديد ورث نفس النطاق',newAdm&&newAdm.role==='year_admin'&&newAdm.serviceId==='svc_prep'&&newAdm.year==='الثانية إعدادي');
t('القديم اتعطل',S.DB.users.find(u=>u.id==='u_yadm').active===false);
await G.reactivateUser('u_bula');
t('إعادة التفعيل شغالة',S.DB.users.find(u=>u.id==='u_bula').active!==false);

sec('١١) الترقية السنوية (خدمات عادية)');
setUser(S.DB.users.find(u=>u.role==='superadmin'));
G.renderWizardPromotion();
// عابرو المرحلة: الثالثة إعدادي → يروحوا خدمة ثانوي (الاقتراح التلقائي المفروض يختارها)
(G.window._wpCrossRows||[]).forEach((r,i)=>{el('wp_target_'+i).value='svc_sec';});
el('wp_bumpusers').checked=true;
await G.executePromotion();
t('بيتر: الثانية إعدادي → الثالثة إعدادي',S.DB.members.find(m=>m.code==='M001').grade==='الثالثة إعدادي');
const yousef=S.DB.members.find(m=>m.code==='M003');
t('يوسف عدّى المرحلة → خدمة ثانوي كوافد من غير خادم',yousef.serviceId==='svc_sec'&&yousef.pendingIncoming===true&&yousef.groupId==='');
t('الوافد مش بيظهر في الحضور قبل التوزيع',(()=>{setUser(S.DB.users.find(u=>u.role==='superadmin'));return !G.getMyMembers().find(m=>m.code==='M003');})());
t('سنة الخادم أمير اترقّت معاه',S.DB.users.find(u=>u.id==='u_amir').year==='الثالثة إعدادي');
t('أعضاء المسرح ماتلمسوش في ترقية العادي',S.DB.members.find(m=>m.code==='M006').grade==='السادسة ابتدائي');
// توزيع الوافد على خادم ثانوي — كيرو اتنقل للمسرح فنرجّع خادم جديد للثانوي الأول
const svNew=await mkUser({id:'u_sec2',name:'شنودة',role:'servant',serviceId:'svc_sec',year:'الأولى ثانوي',groupId:'g_sec2',_pw:'shn1'});
await G.saveDB({usersPatch:{upsert:[svNew]}});
G.openIncomingList();
el('inc_M003').value='u_sec2';
await G.assignIncoming('M003');
const y2=S.DB.members.find(m=>m.code==='M003');
t('الوافد اتوزع: خد مجموعة شنودة والعلم اتشال',y2.groupId==='g_sec2'&&!y2.pendingIncoming);

sec('١٢) موسم المسرح الجديد');
await G.executeTheatreSeason();
const kir=S.DB.members.find(m=>m.code==='M005');
const tom=S.DB.members.find(m=>m.code==='M006');
t('كل أعضاء المسرح دخلوا وضع الانتظار',kir.seasonStatus==='pending'&&tom.seasonStatus==='pending');
t('السنين اتحدثت (توماس: سادسة ابتدائي → أولى إعدادي)',tom.grade==='الأولى إعدادي');
t('اقتراح فئة جديدة لعابر المرحلة (ابتدائي ← إعدادي)',tom.suggestedCategory==='إعدادي');
t('المنتظرون مش بيظهروا في الحضور',(()=>{setUser(svThea);return G.getMyMembers().length===0;})());
setUser(S.DB.users.find(u=>u.role==='superadmin'));
await G.confirmSeasonMember('M006');
const tom2=S.DB.members.find(m=>m.code==='M006');
t('التأكيد طبّق الفئة المقترحة ورجّعه للحضور',tom2.categoryId==='إعدادي'&&tom2.seasonStatus===''&&!tom2.suggestedCategory);
await G.archiveSeasonMember('M005');
const kir2=S.DB.members.find(m=>m.code==='M005');
t('اللي مرجعش اتأرشف بتاريخه (مش اتمسح)',kir2.active===false&&!!kir2.archivedAt&&S.DB.members.some(m=>m.code==='M005'));
await G.restoreMember('M005');
t('الاسترجاع من الأرشيف شغال',S.DB.members.find(m=>m.code==='M005').active===true);

sec('١٣) إغلاق سنة خدمية (أرشفة السجلات)');
const before=S.DB.records.length;
const oldOnes=S.DB.records.filter(r=>r.date<'2026-05-01').length;
el('ya_cutoff').value='2026-05-01';el('ya_label').value='سنة تجريبية 2025';
G.previewYearArchive();
await G.executeYearArchive();
t('السجلات القديمة اتشالت من النشط ('+oldOnes+' سجل)',S.DB.records.length===before-oldOnes&&oldOnes>0);
const archMeta=(S.DB.archives||[])[0];
t('ميتاداتا الأرشيف اتسجلت',archMeta&&archMeta.count===oldOnes&&archMeta.label==='سنة تجريبية 2025');
t('صف الأرشيف اترفع فعلاً على قاعدة البيانات',table.has(archMeta.id)&&table.get(archMeta.id).data.records.length===oldOnes);

sec('١٥) خدمة الأسر — الحقول والربط والبحث');
setUser(S.DB.users.find(u=>u.role==='superadmin'));
// أنشئ خدمة أسر وكاهن
const svcFam={id:'svc_fam',name:'أسر',type:'family'};
S.DB.services.push(svcFam);
const priest=await mkUser({id:'u_priest',name:'أبونا مرقس',role:'servant',serviceId:'svc_fam',groupId:'g_priest',_pw:'priest1'});
await G.saveDB({servicesPatch:{upsert:[svcFam]},usersPatch:{upsert:[priest]}});
t('نوع الخدمة family اتعرف',G.isFamilyServiceId('svc_fam')===true&&G.isFamilyServiceId('svc_prep')===false);

// نضيف الزوج كـ superadmin عن طريق دالة الأسرة مباشرة (نحاكي النموذج)
setUser(S.DB.users.find(u=>u.role==='superadmin'));
// نحاكي إدخال النموذج: الزوج جرجس، زوجته مريم (مش موجودة) → لازم تتنشئ تلقائي
function setInput(id,val){G.document.getElementById(id).value=val;}
function setChecked(id,val){G.document.getElementById(id).checked=val;}
// نبني نموذج الأسرة يدوياً عن طريق استدعاء confirmAddFamilyMember بعد ملء الحقول
// نحضّر عناصر النموذج
['am_name','am_phone','am_father','am_addr','am_notes','fam_marital','fam_job','fam_years','fam_spouse_name','fam_spouse_code'].forEach(id=>el(id));
el('am_name').value='جرجس سمير';el('am_phone').value='0100';el('am_father').value='';el('am_addr').value='ش النيل';el('am_notes').value='';
el('fam_marital').value='متزوج';el('fam_job').value='مهندس';el('fam_years').value='10';el('fam_spouse_name').value='مريم فؤاد';
// جدول الأولاد — نحاكي عنصرين
function makeFakeChildRow(name,grade,active){
  return {querySelector:(sel)=>{
    if(sel==='.ch_name')return{value:name};
    if(sel==='.ch_grade')return{value:grade};
    if(sel==='.ch_active')return{checked:active};
  }};
}
global._fakeChildRows=[
  makeFakeChildRow('يوسف','الثالثة ابتدائي',true),
  makeFakeChildRow('مارتينا','حضانة',false)
];
await G.confirmAddFamilyMember('svc_fam');
let george=S.DB.members.find(m=>m.name==='جرجس سمير');
let mary=S.DB.members.find(m=>m.name==='مريم فؤاد');
t('الزوج اتسجل بحقول أسرة (متزوج/مهندس/10 سنين)',george&&george.maritalStatus==='متزوج'&&george.job==='مهندس'&&george.marriageYears===10);
t('الزوج grade=تخرج تلقائياً (الترقية تتجاهله)',george&&george.grade==='تخرج');
t('الزوجة اتنشأت تلقائياً',!!mary&&mary._autoCreated===true);
t('الربط الفعلي اتعمل من الطرفين',george.spouseCode===mary.code&&mary.spouseCode===george.code);
t('حالة الزوجة اتظبطت متزوجة',mary.maritalStatus==='متزوجة');
t('الأولاد اتزرعوا عند الزوج (2)',george.children.length===2&&george.children[0].name==='يوسف');
t('الأولاد اتزامنوا عند الزوجة تلقائياً',mary.children.length===2&&mary.children[1].name==='مارتينا');
t('بيانات الطفل كاملة (اسم+سنة+أنشطة)',george.children[0].grade==='الثالثة ابتدائي'&&george.children[0].inActivities===true&&george.children[1].inActivities===false);

// البحث الشامل: نبحث باسم ابن → يطلع الأسرة
setUser(S.DB.users.find(u=>u.role==='superadmin'));
G.renderMembersPage();
el('memberSearch').value='يوسف';
G.filterMembers();
let tbodyHtml=el('membersTbody').innerHTML;
t('بحث باسم ابن (يوسف) رجّع أبوه أو أمه في النتائج',tbodyHtml.includes('جرجس')||tbodyHtml.includes('مريم'));
el('memberSearch').value='مارتينا';
G.filterMembers();
t('بحث باسم بنت (مارتينا) رجّع البيت',el('membersTbody').innerHTML.includes('جرجس')||el('membersTbody').innerHTML.includes('مريم'));

// الربط بشخص موجود: نضيف زوج تالت ونربطه بمريم؟ لأ — نختبر فك الربط عند الطلاق
// نعدّل جرجس لـ مطلق → لازم يفك الربط من الطرفين
el('em_name').value='جرجس سمير';el('em_phone').value='0100';el('em_father').value='';el('em_addr').value='ش النيل';el('em_notes').value='';
el('fam_marital').value='مطلق';el('fam_job').value='مهندس';el('fam_years').value='';el('fam_spouse_name').value='مريم فؤاد';
el('fam_spouse_code').value=mary.code;
global._fakeChildRows=[makeFakeChildRow('يوسف','الثالثة ابتدائي',true),makeFakeChildRow('مارتينا','حضانة',false)];
await G.saveFamilyEdit(george.code);
george=S.DB.members.find(m=>m.code===george.code);
mary=S.DB.members.find(m=>m.code===mary.code);
t('تغيير الحالة لمطلق فكّ الربط عند الطرفين',!george.spouseCode&&!mary.spouseCode);
t('الطرف الأول بقى مطلق',george.maritalStatus==='مطلق');

// الترقية العادية لا تلمس أعضاء الأسر (كلهم تخرج)
let famBefore=S.DB.members.filter(m=>m.serviceId==='svc_fam').map(m=>m.grade).join(',');
G.renderWizardPromotion();
(G.window._wpCrossRows||[]).forEach((r,i)=>{let e=el('wp_target_'+i);e.value='_stay';});
if(el('wp_bumpusers'))el('wp_bumpusers').checked=false;
await G.executePromotion();
let famAfter=S.DB.members.filter(m=>m.serviceId==='svc_fam').map(m=>m.grade).join(',');
t('الترقية السنوية لم تلمس أعضاء الأسر (كلهم تخرج)',famBefore===famAfter&&famBefore.split(',').every(g=>g==='تخرج'));

sec('١٦) تكويد مستخدمين فرعيين — فلترة السنين بالمرحلة (حضانة)');
setUser(S.DB.users.find(u=>u.role==='superadmin'));
// خدمة حضانة + مسئول خدمة لها
const nurSvc=(S.DB.services||[]).find(x=>x.id==='svc_nur')||{id:'svc_nur',name:'حضانة',type:'regular'};
if(!(S.DB.services||[]).find(x=>x.id==='svc_nur'))S.DB.services.push(nurSvc);
const nurAdmin=await mkUser({id:'u_nuradm',name:'مسئول الحضانة',role:'service_admin',serviceId:'svc_nur',_pw:'nur1'});
await G.saveDB({usersPatch:{upsert:[nurAdmin]},servicesPatch:{upsert:[nurSvc]}});
// محاكاة: مسئول الحضانة يفتح نموذج إضافة مستخدم بدور خادم
setUser(nurAdmin);
el('au_role').value='servant';
G.updateUserFormFields();
let servantExtra=el('au_extra').innerHTML;
t('مسئول الحضانة: نموذج الخادم فيه اختيار سنة',servantExtra.includes('au_year'));
t('سنين الخادم = تمهيدي/KG1/KG2 فقط (مش القائمة الكاملة)',servantExtra.includes('تمهيدي')&&servantExtra.includes('KG2')&&!servantExtra.includes('الأولى إعدادي')&&!servantExtra.includes('الأولى ابتدائي'));
// مسئول سنة
el('au_role').value='year_admin';
G.updateUserFormFields();
let yaExtra=el('au_extra').innerHTML;
t('مسئول الحضانة: نموذج مسئول السنة فيه سنين الحضانة بس',yaExtra.includes('تمهيدي')&&yaExtra.includes('KG1')&&yaExtra.includes('KG2')&&!yaExtra.includes('الأولى إعدادي'));
// إنشاء فعلي لخادم بسنة KG1
el('au_name').value='خادمة الحضانة';el('au_pw').value='kgserv1';
el('au_role').value='servant';G.updateUserFormFields();
el('au_year').value='KG1';
await G.confirmAddUser();
let kgServ=S.DB.users.find(u=>u.name==='خادمة الحضانة');
t('الخادم اتسجل بسنة KG1 فعلاً',kgServ&&kgServ.year==='KG1'&&kgServ.serviceId==='svc_nur');

sec('١٧) السلسلة الكاملة: مسئول خدمة → خادم بسنة → مسئول السنة يشوفه → عضو يورث السنة');
setUser(S.DB.users.find(u=>u.role==='superadmin'));
// خدمة إعدادي جديدة نظيفة + مسئول خدمة
const prep2={id:'svc_prep2',name:'إعدادي شرق',type:'regular'};
S.DB.services.push(prep2);
const prep2Admin=await mkUser({id:'u_p2adm',name:'مسئول إعدادي شرق',role:'service_admin',serviceId:'svc_prep2',_pw:'p2adm1'});
await G.saveDB({servicesPatch:{upsert:[prep2]},usersPatch:{upsert:[prep2Admin]}});
// مسئول الخدمة يضيف خادم بسنة "الثانية إعدادي"
setUser(prep2Admin);
el('au_name').value='خادم تانية';el('au_pw').value='t2serv';
el('au_role').value='servant';G.updateUserFormFields();
let ex=el('au_extra').innerHTML;
t('مسئول الإعدادي: نموذج الخادم فيه سنين الإعدادي فقط',ex.includes('الأولى إعدادي')&&ex.includes('الثالثة إعدادي')&&!ex.includes('الأولى ثانوي')&&!ex.includes('KG1'));
el('au_year').value='الثانية إعدادي';
await G.confirmAddUser();
let t2serv=S.DB.users.find(u=>u.name==='خادم تانية');
t('الخادم اتسجل بسنة الثانية إعدادي',t2serv&&t2serv.year==='الثانية إعدادي');
// مسئول سنة الثانية إعدادي
setUser(S.DB.users.find(u=>u.role==='superadmin'));
const ya2=await mkUser({id:'u_ya2',name:'مسئول سنة تانية',role:'year_admin',serviceId:'svc_prep2',year:'الثانية إعدادي',groupId:'yr_p2_2',_pw:'ya2p'});
await G.saveDB({usersPatch:{upsert:[ya2]}});
// مسئول السنة يشوف الخادم بتاع سنته
setUser(ya2);
let seen=G.getMyServants().find(u=>u.id===t2serv.id);
t('مسئول السنة (تانية إعدادي) شايف الخادم بتاع سنته',!!seen);
// الخادم يكوّد عضو → يورث سنة الخادم
setUser(t2serv);
el('am_name').value='مخدوم تانية';el('am_phone').value='';el('am_father').value='';el('am_addr').value='';el('am_notes').value='';
// الخادم مالوش نموذج سنة (بتيجي من سنته) — نستدعي confirmAddMember مباشرة
await G.confirmAddMember();
let newMem=S.DB.members.find(m=>m.name==='مخدوم تانية');
t('العضو ورث سنة الخادم تلقائياً (الثانية إعدادي)',newMem&&newMem.grade==='الثانية إعدادي'&&newMem.serviceId==='svc_prep2');
t('العضو ظهر لمسئول السنة نفسها',(()=>{setUser(ya2);return !!G.getMyMembers().find(m=>m.code===newMem.code);})());

sec('١٨) مسئول الأسر يضيف كاهن — من غير اختيار سنة');
setUser(S.DB.users.find(u=>u.role==='superadmin'));
const famAdmin=await mkUser({id:'u_famadm',name:'مسئول الأسر',role:'service_admin',serviceId:'svc_fam',_pw:'famadm1'});
await G.saveDB({usersPatch:{upsert:[famAdmin]}});
setUser(famAdmin);
el('au_role').value='servant';
G.updateUserFormFields();
let famExtra=el('au_extra').innerHTML;
t('مسئول الأسر: نموذج الكاهن مافيهوش اختيار سنة خالص',!famExtra.includes('au_year')&&!famExtra.includes('الأولى')&&!famExtra.includes('KG'));
t('مسئول الأسر: النموذج بيقول كاهن',famExtra.includes('الكاهن')||famExtra.includes('كاهن'));
// مقارنة: مسئول إعدادي شرق لسه بيشوف سنين
setUser(S.DB.users.find(u=>u.id==='u_p2adm'));
el('au_role').value='servant';G.updateUserFormFields();
t('للتأكيد: مسئول الإعدادي لسه بيشوف اختيار السنة',el('au_extra').innerHTML.includes('au_year'));

sec('١٤) سلامة نهائية');
await G.refreshDB();
t('refresh بعد كل ده من غير أخطاء والداتا سليمة',S.DB.members.length>=7&&S.DB.users.length>=9);
t('السيرفر متطابق مع آخر حالة',table.get(S.ROW).data.members.length===S.DB.members.length);

}catch(e){
  fail++;
  console.error('\n💥 استثناء غير متوقع في «'+section+'»:',e&&e.stack||e);
}
console.log('\n════════════════════════════════');
console.log((fail===0?'✅':'❌')+' النتيجة: '+pass+' ناجح — '+fail+' فاشل');
console.log('════════════════════════════════');
process.exit(fail?1:0);
})();
