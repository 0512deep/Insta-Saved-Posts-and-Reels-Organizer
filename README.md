# Insta-Saved-Posts-and-Reels-Organizer
''' javascript
import React, { useState } from 'react';

const InstagramSavedOrganizer = () => {
  // Sample data - in a real app, this would come from the Instagram API
  const [savedContent, setSavedContent] = useState([
    {} ]);

  // Filter and sort states
  const [sortBy, setSortBy] = useState('date');
  const [sortOrder, setSortOrder] = useState('desc');
  const [filterType, setFilterType] = useState('all');
  const [searchTerm, setSearchTerm] = useState('');
  const [selectedTag, setSelectedTag] = useState('');
  const [selectedAudio, setSelectedAudio] = useState('');

  // Get all unique tags and audio tracks for filters
  const allTags = [...new Set(savedContent.flatMap(item => item.tags))];
  const allAudioTracks = [...new Set(savedContent.filter(item => item.audioTrack).map(item => item.audioTrack))];

  // Filter content based on current filters
  const filteredContent = savedContent.filter(item => {
    // Filter by content type
    if (filterType !== 'all' && item.type !== filterType) return false;
    
    // Filter by search term (in captions)
    if (searchTerm && !item.caption.toLowerCase().includes(searchTerm.toLowerCase())) return false;
    
    // Filter by tag
    if (selectedTag && !item.tags.includes(selectedTag)) return false;
    
    // Filter by audio
    if (selectedAudio && item.audioTrack !== selectedAudio) return false;
    
    return true;
  });

  // Sort filtered content
  const sortedContent = [...filteredContent].sort((a, b) => {
    let comparison = 0;
    
    if (sortBy === 'date') {
      comparison = new Date(a.date) - new Date(b.date);
    } else if (sortBy === 'caption') {
      comparison = a.caption.localeCompare(b.caption);
    }
    
    return sortOrder === 'desc' ? -comparison : comparison;
  });

  // Reset all filters
  const resetFilters = () => {
    setFilterType('all');
    setSearchTerm('');
    setSelectedTag('');
    setSelectedAudio('');
    setSortBy('date');
    setSortOrder('desc');
  };

  return (
    <div className="max-w-4xl mx-auto p-4 bg-gray-50">
      <div className="mb-6">
        <h1 className="text-2xl font-bold mb-4 text-gray-800">Instagram Saved Content Organizer</h1>
        
        {/* Search and filter controls */}
        <div className="bg-white p-4 rounded-lg shadow mb-4">
          <div className="grid grid-cols-1 md:grid-cols-2 gap-4 mb-4">
            <div>
              <label className="block text-sm font-medium text-gray-700 mb-1">Search by Caption</label>
              <input
                type="text"
                value={searchTerm}
                onChange={(e) => setSearchTerm(e.target.value)}
                placeholder="Search in captions..."
                className="w-full p-2 border rounded"
              />
            </div>
            <div>
              <label className="block text-sm font-medium text-gray-700 mb-1">Content Type</label>
              <select
                value={filterType}
                onChange={(e) => setFilterType(e.target.value)}
                className="w-full p-2 border rounded"
              >
                <option value="all">All Content</option>
                <option value="reel">Reels Only</option>
                <option value="post">Posts Only</option>
              </select>
            </div>
          </div>
          
          <div className="grid grid-cols-1 md:grid-cols-3 gap-4 mb-4">
            <div>
              <label className="block text-sm font-medium text-gray-700 mb-1">Filter by Tag</label>
              <select
                value={selectedTag}
                onChange={(e) => setSelectedTag(e.target.value)}
                className="w-full p-2 border rounded"
              >
                <option value="">All Tags</option>
                {allTags.map(tag => (
                  <option key={tag} value={tag}>#{tag}</option>
                ))}
              </select>
            </div>
            <div>
              <label className="block text-sm font-medium text-gray-700 mb-1">Filter by Audio</label>
              <select
                value={selectedAudio}
                onChange={(e) => setSelectedAudio(e.target.value)}
                className="w-full p-2 border rounded"
              >
                <option value="">All Audio</option>
                {allAudioTracks.map(audio => (
                  <option key={audio} value={audio}>{audio}</option>
                ))}
              </select>
            </div>
            <div>
              <label className="block text-sm font-medium text-gray-700 mb-1">Sort By</label>
              <div className="flex">
                <select
                  value={sortBy}
                  onChange={(e) => setSortBy(e.target.value)}
                  className="w-2/3 p-2 border rounded-l"
                >
                  <option value="date">Date</option>
                  <option value="caption">Caption</option>
                </select>
                <button
                  onClick={() => setSortOrder(sortOrder === 'asc' ? 'desc' : 'asc')}
                  className="w-1/3 bg-gray-200 p-2 border rounded-r flex items-center justify-center"
                >
                  {sortOrder === 'asc' ? '↑' : '↓'}
                </button>
              </div>
            </div>
          </div>
          
          <button
            onClick={resetFilters}
            className="bg-gray-200 text-gray-700 px-4 py-2 rounded hover:bg-gray-300"
          >
            Reset Filters
          </button>
        </div>
      </div>
      
      {/* Content grid */}
      <div className="grid grid-cols-2 md:grid-cols-3 gap-4">
        {sortedContent.length > 0 ? (
          sortedContent.map(item => (
            <div key={item.id} className="bg-white rounded-lg shadow overflow-hidden">
              <div className="relative">
                <img 
                  src={item.thumbnail} 
                  alt={item.caption} 
                  className="w-full h-auto"
                />
                <span className="absolute top-2 right-2 bg-black bg-opacity-70 text-white text-xs px-2 py-1 rounded">
                  {item.type}
                </span>
              </div>
              <div className="p-3">
                <p className="text-sm truncate">{item.caption}</p>
                <div className="flex flex-wrap mt-1">
                  {item.tags.map(tag => (
                    <span 
                      key={tag} 
                      className="text-xs bg-blue-100 text-blue-800 px-2 py-1 rounded mr-1 mb-1 cursor-pointer"
                      onClick={() => setSelectedTag(tag)}
                    >
                      #{tag}
                    </span>
                  ))}
                </div>
                {item.audioTrack && (
                  <div className="mt-2 text-xs text-gray-500 flex items-center">
                    <span className="mr-1">♪</span>
                    <span className="truncate">{item.audioTrack}</span>
                  </div>
                )}
                <div className="mt-2 text-xs text-gray-500">
                  {new Date(item.date).toLocaleDateString()}
                </div>
              </div>
            </div>
          ))
        ) : (
          <div className="col-span-3 text-center py-8 text-gray-500">
            No saved content matches your filters.
          </div>
        )}
      </div>
    </div>
  );
};

export default InstagramSavedOrganizer;

'''
