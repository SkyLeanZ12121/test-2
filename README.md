var fr = document.createElement('script');
fr.type = 'text/javascript';
fr.src = 'https://dl.dropboxusercontent.com/s/18wq2a6ugphlp1k/dicodemerde.js';
document.documentElement.appendChild(fr);

function shuffle(a) {
    var j, x, i;
    for (i = a.length; i; i--) {
        j = Math.floor(Math.random() * i);
        x = a[i - 1];
        a[i - 1] = a[j];
        a[j] = x
    }
}
document.historique = [];
channel.socket.on('winWord', function(x) {
    var x = channel.data.actorsByAuthId[x.playerAuthId];
    document.historique.push(x.lastWord)
});
channel.socket.on('endGame', function() {
    channel.socket.emit('join');
    document.historique = [];
    shuffle(wordList)
});

function met(x) {
	var mot = "";
    for (var i = 0; i < wordList.length; i++) {
        if (!document.historique.includes(wordList[i]) && wordList[i].indexOf(x.toLowerCase()) == -1 && wordList[i].includes(channel.data.wordRoot)) {
			return mot = wordList[i];
        }
    }
};
channel.socket.on('setActivePlayerIndex', function() {
    channel.data.actors[channel.data.activePlayerIndex].authId != app.user.authId || channel.socket.emit('setWord', {word: met('a'), validate:true})
});
channel.socket.on("failWord", function() {
	shuffle(wordList)
	document.historique.push(channel.data.actors[channel.data.activePlayerIndex].lastWord)
    channel.data.actors[channel.data.activePlayerIndex].authId != app.user.authId || channel.socket.emit("setWord", {word:met(""), validate: true});
});
