/*
	Add Admin user and media folders on first start
 */
module.exports = {
	/*
		Default Admin user
	*/
	adminUser: {
		name: 'admin',
		/*
		   Since the default admin password is stored in clear in this file,
		   you MUST change it on first login
		*/
		pass: 'admin'
	},
	/*
		Default Media folders
		Scan strategies:
		'auto' -- try to figure it out
		'artistalbum' -- artist/album folder structure
		'compilation' -- bunch of compilation folders
		'audiobook' -- bunch of audiobook folders
	 */
	roots: [
		{name: 'Music', path: '/usr/share/media/music', strategy: 'auto'},
		{name: 'Compilations', path: '/usr/share/media/compilations', strategy: 'compilation'},
		{name: 'Audiobooks', path: '/usr/share/media/audiobooks', strategy: 'audiobook'},
		{name: 'Soundtracks', path: '/usr/share/media/soundtrack', strategy: 'compilation'}
	]
};
