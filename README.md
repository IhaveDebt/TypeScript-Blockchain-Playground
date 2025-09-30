/**
 * blockchain_playground.ts
 *
 * Minimal blockchain with block creation, lightweight PoW, and naive P2P propagation simulation.
 *
 * Run:
 *   ts-node src/blockchain_playground.ts
 */
import crypto from 'crypto';
type Block = { index:number; prev:string; ts:number; nonce:number; data:string; hash:string };

class Chain {
  blocks: Block[] = [];
  constructor(){ this.blocks.push(this.genesis()); }
  genesis(){ const b={index:0,prev:'0',ts:Date.now(),nonce:0,data:'gen',hash:''}; b.hash=this.hash(b); return b;}
  hash(b:Partial<Block>){ return crypto.createHash('sha256').update(JSON.stringify(b)).digest('hex'); }
  mine(data:string,diff=1){
    const prev=this.blocks[this.blocks.length-1];
    let nonce=0; let h='';
    while(true){
      const b={index:prev.index+1,prev:prev.hash,ts:Date.now(),nonce,data,hash:''};
      h=this.hash(b);
      if (h.startsWith('0'.repeat(diff))) { b.hash=h; this.blocks.push(b as Block); break; }
      nonce++; if (nonce%100000===0) break; // safety
    }
    return this.blocks[this.blocks.length-1];
  }
}

(function demo(){
  const c = new Chain();
  console.log('Mining block...');
  c.mine('tx:alice->bob:10',2);
  console.log('Chain length', c.blocks.length);
  console.log('Last hash', c.blocks[c.blocks.length-1].hash.slice(0,8));
})();
