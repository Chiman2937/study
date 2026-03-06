```js
function solution(maps) {
    const rows = maps.length, cols = maps[0].length;
    if (maps[0][0] === 0 || maps[rows-1][cols-1] === 0) return -1;
    
    const directions = [[-1,0], [1,0], [0,-1], [0,1]];
    const visited = new Set([0]);
    const queue = [[1, 0]];
    
    for (let i = 0; i < queue.length; i++) {
        const [dist, pos] = queue[i];
        const r = Math.floor(pos/cols), c = pos%cols;
        
        if (r === rows-1 && c === cols-1) return dist;
        
        for (const [dr, dc] of directions) {
            const nr = r+dr, nc = c+dc;
            const npos = nr*cols + nc;
            
            if (nr>=0 && nr<rows && nc>=0 && nc<cols && 
                maps[nr][nc] === 1 && !visited.has(npos)) {
                visited.add(npos);
                queue.push([dist + 1, npos]);
            }
        }
    }
    
    return -1;
}
```
